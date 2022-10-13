#msbuild 

---

[Learn.MS](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-well-known-item-metadata?view=vs-2022)

```xml
<ItemGroup>
    <MyItem Include="Source\Program.cs" />
</ItemGroup>
```

Item metadata|Description|Result
--|--|--
%(FullPath)|Contains the full path of the item.|C:\\MyProject\\Source\\Program.cs
%(RootDir)|Contains the root directory of the item.|C:\\
%(Filename)|Contains the file name of the item, without the extension.|Program
%(Extension)|Contains the file name extension of the item.|.cs
%(RelativeDir)|Contains the path specified in the `Include` attribute, up to the final backslash (\\)|.Source\\
%(Directory)|Contains the directory of the item, without the root directory.|MyProject\\Source\\
%(RecursiveDir)|Expands __\*\*__ wildcard in the path.|[[#RecursiveDir]]
%(Identity)|The item specified in the `Include` attribute.|_Source\Program.cs_
%(ModifiedTime)|Contains the timestamp from the last time the item was modified.|2004-07-01 00:21:31.5073316
%(CreatedTime)|Contains the timestamp from when the item was created.|2004-06-25 09:26:45.8237425
%(AccessedTime)|Contains the timestamp from the last time the item was accessed.2004-08-14 16:52:36.3168743

#### RecursiveDir

If the `Include` attribute contains the wildcard __\*\*__, this metadata specifies the part of the path that replaces the wildcard. 
For more information on wildcards, see [How to: Select the files to build](https://learn.microsoft.com/en-us/visualstudio/msbuild/how-to-select-the-files-to-build?view=vs-2022).  
  
If the folder _C:\\MySolution\\MyProject\\Source\\_ contains the file _Program.cs_, and if the project file contains this item:  

```xml
<ItemGroup>`  
	<MyItem Include="C:\**\Program.cs" />`  
</ItemGroup>`  
```

then the value of `%(MyItem.RecursiveDir)` would be _MySolution\\MyProject\\Source\\_.
