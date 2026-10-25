# escape=`
FROM mcr.microsoft.com/windows/servercore:ltsc2022

SHELL ["cmd", "/S", "/C"]

RUN powershell -NoLogo -ExecutionPolicy Bypass -Command "$ErrorActionPreference='Stop'; $ProgressPreference='SilentlyContinue'; Invoke-WebRequest -Uri 'https://aka.ms/vs/17/release/vs_buildtools.exe' -OutFile 'C:\vs_buildtools.exe'; $installArgs=@('--quiet','--wait','--norestart','--nocache','--installPath','C:\BuildTools','--add','Microsoft.VisualStudio.Workload.VCTools','--includeRecommended','--add','Microsoft.VisualStudio.Component.VC.CMake.Project'); $installer=Start-Process -FilePath 'C:\vs_buildtools.exe' -ArgumentList $installArgs -Wait -PassThru; if ($installer.ExitCode -ne 0 -and $installer.ExitCode -ne 3010) { throw ('Visual Studio Build Tools install failed: ' + $installer.ExitCode) }; Remove-Item 'C:\vs_buildtools.exe' -Force"

ENV PATH="C:\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin;${PATH}"

WORKDIR C:\app
COPY . .

RUN cmake -S . -B build -G "Visual Studio 17 2022" -A x64 && cmake --build build --config Release --parallel

CMD ["C:\\app\\build\\bin\\Release\\ThreadDemo.exe"]