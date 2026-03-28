## Boxes I watched:
### Mailing
1. I learned that we can verify if the file is static or dynamic with usage of `wget`. If the modify date is the same with the access date while running `stat` command, we know that the file is dynamic. if the dates are different, so it is static. because the file is created there and never being touched.
2. An LFI in windows, `../../../../../windows/system32/license.rtf` should be checked.
3. The nmap result showed that they use `hmailserver` so, he tried to find its docs and structure and disclose those files.
4. The short name of `Program Files` is `progra~1` and `progra~2` in windows
5. He used `swaks` to authenticate in a SMTP server:![[swak.png]]
6. One idea could be to email any one and see, if they click on them?
7.  Ipp said we might be able to leak NTML hash with LFI as well!
8. 