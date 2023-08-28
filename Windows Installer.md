#windows/installer

---

# Command line

## How to: Uninstall app

1. Run command prompt as administrator.
2. Type **wmic** and hit enter.
3. Type **product get name** and hit enter. This will show a list of all installed programs.
4. Find the Java version – I think mine was something like Java 8 Update 371.
5. Run the command: **product where name="program name" call uninstall**, where “program name” is the version of Java you want to uninstall. So, it should look like: **product where name="Java 8 Update 371" call uninstall**.
6. Hit Y to confirm and press enter. You should see “Method execution successful”. Java should now be uninstalled.