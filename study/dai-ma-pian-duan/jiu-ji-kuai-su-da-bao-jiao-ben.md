# 究极快速打包脚本

```bash
@ECHO OFF&PUSHD %~DP0 &TITLE 究极快速打包脚本 - By Lior Base 康鑫
mode con cols=58 lines=40
color 02

rem 目标路径
set TARGET_PATH=D:\JAR\LN-SPORT
rem JAR包源路径
set JAR_SOURCE_PATH=F:\WorkSpace\LiNing\2021Sports
rem static源路径
set STATIC_SOURCE_PATH=F:\WorkSpace\LiNing\2021Sports\sports-manage\dist
rem 7z路径
set 7z=C:\Program Files\7-Zip\7z.exe
rem uat前端包名
set STATIC_ARCHIVE_NAME_UAT=sc-qas-pc-static
rem uat后端包名
set JAR_ARCHIVE_NAME_UAT=sc-qas
rem prd前端包名
set STATIC_ARCHIVE_NAME_PRD=sc-prd-pc-static
rem prd后端包名
set JAR_ARCHIVE_NAME_PRD=sc-prd

rem ftp地址
set FTP_IP=139.224.239.30
rem ftp用户名
set USERNAME=upload
rem ftp密码 %需要转义，即想要输入%，需要输入%%
set PASSWORD=SGKi023*%%#140SK
rem 上传目标路径 UAT
set FTP_PATH_UAT=image/sc/qas
rem 上传目标路径 PRD
set FTP_PATH_PRD=image/sc/prd
rem 准备上传的本地文件路径 UAT
set UPLOAD_FILE_PATH_UAT=%TARGET_PATH%\%JAR_ARCHIVE_NAME_UAT%.tar.gz
rem 准备上传的本地文件路径 PRD
set UPLOAD_FILE_PATH_PRD=%TARGET_PATH%\%JAR_ARCHIVE_NAME_PRD%.tar.gz

set TOAST=李宁运动中心代码已上传，预祝部署成功！( •̀ ω •́ )y

:menu
cls
echo.======================================================
echo                        MENU
echo ======================================================
echo.
echo ――――――――――【1】李宁运动中心 UAT   ―――――――
echo.
echo ――――――――――【2】李宁运动中心 PRD   ―――――――
echo.
echo ――――――――――      其他命令退出      ―――――――
echo.
echo ======================================================

set /p user_input=选择并进入命令：
if %user_input%==1 goto lnsport-uat
if %user_input%==2 goto lnsport-prd
if %user_input%=="" goto exit
if not %user_input%=="" goto exit

:lnsport-uat
echo y | del "%TARGET_PATH%\*"

echo 压缩并复制李宁运动中心项目static包.........
7z a "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_UAT%.tar" "%STATIC_SOURCE_PATH%\**"
7z a "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_UAT%.tar.gz" "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_UAT%.tar"
echo y | del "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_UAT%.tar"

echo 复制李宁运动中心项目JAR包.........
echo.  

echo f | xcopy "%JAR_SOURCE_PATH%\sports-gateway\target\sports-gateway-2.0.0.jar" "%TARGET_PATH%\sports-gateway-2.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-basic\sports-basic-application\target\sports-basic-application-1.0.0.jar" "%TARGET_PATH%\sports-basic-application-1.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-exercise\sports-exercise-application\target\sports-exercise-application-1.0.0.jar" "%TARGET_PATH%\sports-exercise-application-1.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-interface\sports-interface-application\target\sports-interface-application-1.0.0.jar" "%TARGET_PATH%\sports-interface-application-1.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-job\sports-job-admin\target\sports-job-admin-2.2.1-SNAPSHOT.jar" "%TARGET_PATH%\sports-job-admin-2.2.1-SNAPSHOT.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-job\sports-job-executor\target\sports-job-executor-2.2.1-SNAPSHOT.jar" "%TARGET_PATH%\sports-job-executor-2.2.1-SNAPSHOT.jar" /e /c /y /h /r

echo.  
echo 压缩李宁运动中心项目JAR包.........
echo.  

7z a "%TARGET_PATH%\%JAR_ARCHIVE_NAME_UAT%.tar" "%TARGET_PATH%\**"
7z a "%TARGET_PATH%\%JAR_ARCHIVE_NAME_UAT%.tar.gz" "%TARGET_PATH%\%JAR_ARCHIVE_NAME_UAT%.tar"
echo y | del "%TARGET_PATH%\%JAR_ARCHIVE_NAME_UAT%.tar"
echo 李宁运动中心【UAT环境】打包完成！

echo.  
echo 上传FTP中.........
echo.  

echo open %FTP_IP% > ftp.command
echo %USERNAME%>>ftp.command
echo %PASSWORD%>>ftp.command
echo cd %FTP_PATH_UAT% >>ftp.command
echo binary>>ftp.command
echo put %UPLOAD_FILE_PATH_UAT%>>ftp.command
echo bye>>ftp.command
ftp -s:ftp.command

echo.
msg * %TOAST%
echo. & pause
goto menu

:lnsport-prd
echo y |del "%TARGET_PATH%\*"

echo 压缩并复制李宁运动中心项目static包.........
7z a "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_PRD%.tar" "%STATIC_SOURCE_PATH%\**"
7z a "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_PRD%.tar.gz" "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_PRD%.tar"
echo y | del "%TARGET_PATH%\%STATIC_ARCHIVE_NAME_PRD%.tar"

echo 复制李宁运动中心项目JAR包.........
echo.  

echo f | xcopy "%JAR_SOURCE_PATH%\sports-gateway\target\sports-gateway-2.0.0.jar" "%TARGET_PATH%\sports-gateway-2.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-basic\sports-basic-application\target\sports-basic-application-1.0.0.jar" "%TARGET_PATH%\sports-basic-application-1.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-exercise\sports-exercise-application\target\sports-exercise-application-1.0.0.jar" "%TARGET_PATH%\sports-exercise-application-1.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-interface\sports-interface-application\target\sports-interface-application-1.0.0.jar" "%TARGET_PATH%\sports-interface-application-1.0.0.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-job\sports-job-admin\target\sports-job-admin-2.2.1-SNAPSHOT.jar" "%TARGET_PATH%\sports-job-admin-2.2.1-SNAPSHOT.jar" /e /c /y /h /r
echo f | xcopy "%JAR_SOURCE_PATH%\sports-job\sports-job-executor\target\sports-job-executor-2.2.1-SNAPSHOT.jar" "%TARGET_PATH%\sports-job-executor-2.2.1-SNAPSHOT.jar" /e /c /y /h /r

echo.  
echo 压缩李宁运动中心项目JAR包.........
echo.  

7z a "%TARGET_PATH%\%JAR_ARCHIVE_NAME_PRD%.tar" "%TARGET_PATH%\**"
7z a "%TARGET_PATH%\%JAR_ARCHIVE_NAME_PRD%.tar.gz" "%TARGET_PATH%\%JAR_ARCHIVE_NAME_PRD%.tar"
echo y | del "%TARGET_PATH%\%JAR_ARCHIVE_NAME_PRD%.tar"

echo 李宁运动中心【PRD环境】打包完成！

echo.  
echo 上传FTP中.........
echo.  

echo open %FTP_IP% > ftp.command
echo %USERNAME%>>ftp.command
echo %PASSWORD%>>ftp.command
echo cd %FTP_PATH_PRD% >>ftp.command
echo binary>>ftp.command
echo put %UPLOAD_FILE_PATH_PRD%>>ftp.command
echo bye>>ftp.command
ftp -s:ftp.command

echo.
msg * %TOAST%
echo. & pause
goto menu
```



