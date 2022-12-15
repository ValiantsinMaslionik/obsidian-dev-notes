#tools

---

## Exit codes

```txt
Default exit codes:
0 : Success
-1 : Parameters validation error
-2 : Command execution error
-3 : Target application returned non-zero exit code
-4 : Command execution timeout
-10: Unhandled exception

This information is available in dotCover console runner help via "dotCover.exe help exitcodes" command
```

## Automapper issue

https://youtrack.jetbrains.com/issue/DCVR-9763
https://youtrack.jetbrains.com/issue/TW-61037

As a workaround could you please try to add *--DisableNGen* parameter to *dotCover* command line?

