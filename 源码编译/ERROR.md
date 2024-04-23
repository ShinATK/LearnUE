
.\MyUE\Engine\Plugins\Runtime\Steam\SteamVR\Source\SteamVRInputDevice\Private\SteamVRInputDeviceFunctionLibrary.cpp
// TODO: 关掉“error C4834: 放弃具有 [[nodiscard]] 属性的函数的返回值”
#pragma warning(disable:4834)


.\MyUE\Engine\Plugins\Runtime\AR\Microsoft\HoloLensAR\Source\HoloLensTargetPlatform\HoloLensTargetPlatform.Build.cs
// TODO: 关掉“C4668 没有将“_NOEXCEPT_TYPES_SUPPORTED”定义为预处理器宏，用“0”替换“#if/#elif” ”
bEnableUndefinedIdentifierWarnings = false;


1>C:\Program Files\Microsoft Visual Studio\2022\Community\MSBuild\Microsoft\VC\v170\Microsoft.MakeFile.Targets(44,5): error MSB3073: 命令“..\..\Build\BatchFiles\Build.bat -Target="UE4Editor Win64 Development" -Target="ShaderCompileWorker Win64 Development -Quiet" -WaitMutex -FromMsBuild”已退出，代码为 6。

// TODO:
删除引擎目录下：\Engine\Intermediate\Build\Win64\UE4Editor\Development\VisualStudioDTE

