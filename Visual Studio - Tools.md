#VisualStudio/Tools/2019
#VisualStudio/Tools/2022

---

# Performance profiling

## IDE

[Analyze CPU usage without debugging](https://learn.microsoft.com/en-us/visualstudio/profiling/cpu-usage?view=vs-2022)

## Command line

[Measure application performance from the command line](https://learn.microsoft.com/en-us/visualstudio/profiling/profile-apps-from-command-line?view=vs-2022)

# C# Interactive

https://dzone.com/articles/c-interactive-in-visual-studio

We can execute any code block in the Interactive Window. For this, we have to just select the method which we want to test/access in the C# Interactive Window. Check the below example, I have created a Calculator class which has an Add, Subtract, Multiply, and Divide method.

![Image title](https://3.bp.blogspot.com/-9lzFRMqOs4Q/W4wnzzMc45I/AAAAAAAABsc/PlwtNt7vHHoELx6KdD0QJG09j8USiHavACLcBGAs/s1600/image9.png)

Now select the method or function you want to test in the C# Interactive window and right-click and select Execute in Interactive.

![Image title](https://4.bp.blogspot.com/-Sul8Re7iV78/W4woGGyfVaI/AAAAAAAABso/p9ZEEkx3he4Ch-qsHIAUHEo_wTu31anxgCLcBGAs/s1600/image10.png)

Test the method by passing the parameter value in the method.

![Image title](https://4.bp.blogspot.com/-z15vv810Jvk/W4woU0MlLwI/AAAAAAAABss/qnSa2AYwjwouABfWnnvBTmAthRfjTvZJACLcBGAs/s1600/image11.png)

We can also load the DLL in REPL with the help of the `#r` command, followed by the path of the DLL to load in the C# Interactive Window.

![Image title](https://1.bp.blogspot.com/-1wq6AsuDzdI/W4worQdOryI/AAAAAAAABs4/en2oqM3xVW82OcCZ-G3cAJ6sGIADDi--QCLcBGAs/s1600/image12.png)

C# Interactive Window also supports error handling in both compile time as well as runtime. Check the below screenshot:

![Image title](https://1.bp.blogspot.com/-038dUrKMjYE/W4wo9Rd6BrI/AAAAAAAABtA/E4mGs_NyZOMEaCHY7ZM89YyWNmEwNYCbwCLcBGAs/s1600/image13.png)

Hope this will help you. Thanks!