# How to use a custom GC

To try out a custome GC implmentation the CLR supports a way to plugin a `standalone` gc. This readme briefly describe how to use a custom GC.

## Figure out the base directory (required for .net 8 or below only)

You need to know where is the `coreclr` module is actually used in your process. In case of self-contained deployment, it is right next to the executable, otherwise, it is usually stored in the global installation directory, for example, on Windows, it will be something like this:

`C:\Program Files\dotnet\shared\Microsoft.NETCore.App\8.0.8\coreclr.dll` for windows 
`/usr/share/dotnet/shared/Microsoft.NETCore.App/8.0.0/libcoreclr.so` on linux

## Configure the runtime

For .NET 9 or above, you can set use the GCPath configuration described [here](https://learn.microsoft.com/en-us/dotnet/core/runtime-config/garbage-collector#path). As described in the doc, the configuration should be a full path to the clrgc module.

Otherwise, you will need to use the GCName configuration described [here](https://learn.microsoft.com/en-us/dotnet/core/runtime-config/garbage-collector#name). As described in the doc, the configuration should be a relative path from the coreclr module to the clrgc module.

In both cases, if you could change the configuration file, use the configuration file is easiest. This will make sure you impact exactly only the processes that have the config file changed. Alternatively, you can use the environment variable option as well, be careful that any subprocesses that get launched will also be impacted.

## Making sure the custom GC is used

If you could look at the module list (using a dump or otherwise), you can check that the clrgc module is loaded.

Alternatively, if it is easier, we can check the result of the `GC.GetConfigurationVariable()` API. One of the entries in the API should be `GCName:clrgcexp.dll` or something like that depending how you name the module and platforms.
