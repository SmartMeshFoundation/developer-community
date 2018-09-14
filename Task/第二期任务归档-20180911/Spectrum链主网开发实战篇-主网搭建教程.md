作者: 唐爽 校阅者: rectinajh
<div data-note-content="" class="show-content">
          <div class="show-content-free">
            <p>系统版本： win7 64位</p><hr><p>所需工具</p><p>1. Go&nbsp;version 1.7 或更高版本</p><p>2. GCC</p><hr><p>安装Go:</p><p>完整下载地址: https://dl.google.com/go/go1.10.2.windows-amd64.msi</p><p><br></p><div class="image-package  ">
<div class="image-container" style="max-width: 700px; max-height: 600px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 77.72%;"></div>
<div class="image-view" data-width="772" data-height="600"><img  src="https://upload-images.jianshu.io/upload_images/3176928-d95c6c97e3e3db5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/772/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><p>安装的路径为 C:\Go\目录下. 添加&nbsp; &nbsp;C:\Go\bin到环境变量中.</p><p><br></p><hr><p>安装GCC编译环境</p><p>下载地址：&nbsp;https://jaist.dl.sourceforge.net/project/mingw-w64/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe</p><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 578px;">
<div class="image-container-fill" style="padding-bottom: 73.26%;"></div>
<div class="image-view" data-width="789" data-height="578"><img src="https://upload-images.jianshu.io/upload_images/3176928-3c955162873fc4b0.png" data-original-width="789" data-original-height="578" data-original-format="image/png" data-original-filesize="148459" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 589px;">
<div class="image-container-fill" style="padding-bottom: 73.53%;"></div>
<div class="image-view" data-width="801" data-height="589"><img src="https://upload-images.jianshu.io/upload_images/3176928-4ae1840d9222d9bf.png" data-original-width="801" data-original-height="589" data-original-format="image/png" data-original-filesize="107000" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><p>添加 bin目录到环境变量中. win7，笔者默认路径在C:\mingw-w64\x86_64-7.1.0-win32-sjlj-rt_v5-rev0\mingw64\bin</p><p><br></p><hr><p>测试是否安装成功.</p><p>命令行模式下：</p><p>1. go version</p><p>2. gcc</p><p>如果有显示则表示安装和配置成功.</p><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 550px;">
<div class="image-container-fill" style="padding-bottom: 65.32%;"></div>
<div class="image-view" data-width="842" data-height="550"><img src="https://upload-images.jianshu.io/upload_images/3176928-869ec630dff0bfc7.png" data-original-width="842" data-original-height="550" data-original-format="image/png" data-original-filesize="69932" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><p><br></p><hr><p>编译 Spectrum链</p><p>下载源文件</p><p>下载地址:https://github.com/SmartMeshFoundation/Spectrum</p><p>将代码下载后解压到以下目录:</p><p>C:\Go\src\github.com\SmartMeshFoundation\Spectrum</p><p>执行命令: go install -v ./cmd/smc</p><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 542px;">
<div class="image-container-fill" style="padding-bottom: 64.91%;"></div>
<div class="image-view" data-width="835" data-height="542"><img src="https://upload-images.jianshu.io/upload_images/3176928-313b98e3dc630791.png" data-original-width="835" data-original-height="542" data-original-format="image/png" data-original-filesize="83045" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><hr><h1>启动网络</h1><p>smc --datadir d:/specturm/data&nbsp; console</p><p>smc console，表示启动节点并进入交互式控制台，--datadir选项指定使用spectrum作为数据的目录</p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 494px;">
<div class="image-container-fill" style="padding-bottom: 70.6%;"></div>
<div class="image-view" data-width="1646" data-height="1162"><img src="https://upload-images.jianshu.io/upload_images/3176928-b3883f53588326cb.png" data-original-width="1646" data-original-height="1162" data-original-format="image/png" data-original-filesize="310418" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><p>personal.newAccount()创建账户</p><p>相应的keystore会生成在data目录.</p><div class="image-package">
<div class="image-container" style="max-width: 599px; max-height: 115px;">
<div class="image-container-fill" style="padding-bottom: 19.2%;"></div>
<div class="image-view" data-width="599" data-height="115"><img src="https://upload-images.jianshu.io/upload_images/3176928-b80cd45b553e7ad3.png" data-original-width="599" data-original-height="115" data-original-format="image/png" data-original-filesize="10485" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><div class="image-package">
<div class="image-container" style="max-width: 570px; max-height: 172px;">
<div class="image-container-fill" style="padding-bottom: 30.18%;"></div>
<div class="image-view" data-width="570" data-height="172"><img src="https://upload-images.jianshu.io/upload_images/3176928-6478da629055ee1e.png" data-original-width="570" data-original-height="172" data-original-format="image/png" data-original-filesize="10560" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><h1>导入钱包:</h1><p>下载smartmesh APP,安装</p><p>https://smartmesh.io/app/</p><p>复制keystore，导入到钱包.</p><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 1245px;">
<div class="image-container-fill" style="padding-bottom: 177.87%;"></div>
<div class="image-view" data-width="750" data-height="1334"><img src="https://upload-images.jianshu.io/upload_images/3176928-4ec61e7f926e1d16.png" data-original-width="750" data-original-height="1334" data-original-format="image/png" data-original-filesize="275051" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 1245px;">
<div class="image-container-fill" style="padding-bottom: 177.87%;"></div>
<div class="image-view" data-width="750" data-height="1334"><img src="https://upload-images.jianshu.io/upload_images/3176928-985196d152b12e43.png" data-original-width="750" data-original-height="1334" data-original-format="image/png" data-original-filesize="348603" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><h1>转账测试</h1><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 1245px;">
<div class="image-container-fill" style="padding-bottom: 177.87%;"></div>
<div class="image-view" data-width="750" data-height="1334"><img src="https://upload-images.jianshu.io/upload_images/3176928-6460c1e2a3d5ac70.png" data-original-width="750" data-original-height="1334" data-original-format="image/png" data-original-filesize="211586" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><p><br></p><p>区块的速度确认相当快速，差不多5分钟就到账了，在APP中则基本上提交成功后就能显示到账记录.</p><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 1245px;">
<div class="image-container-fill" style="padding-bottom: 177.87%;"></div>
<div class="image-view" data-width="750" data-height="1334"><img src="https://upload-images.jianshu.io/upload_images/3176928-9377e15b27e21956.png" data-original-width="750" data-original-height="1334" data-original-format="image/png" data-original-filesize="246104" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 1245px;">
<div class="image-container-fill" style="padding-bottom: 177.87%;"></div>
<div class="image-view" data-width="750" data-height="1334"><img src="https://upload-images.jianshu.io/upload_images/3176928-c7919e2c85afd5aa.png" data-original-width="750" data-original-height="1334" data-original-format="image/png" data-original-filesize="351039" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div><p>控制台上用eth.getBalance('93d8eb9ca297d7a08ff12f709378c7502a0e6aef')查询也显示已经到账</p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 46px;">
<div class="image-container-fill" style="padding-bottom: 6.1899999999999995%;"></div>
<div class="image-view" data-width="743" data-height="46"><img src="https://upload-images.jianshu.io/upload_images/3176928-9a45f69e46e08624.png" data-original-width="743" data-original-height="46" data-original-format="image/png" data-original-filesize="6445" class="image-loading" style="cursor: zoom-in;"></div>
</div>
<div class="image-caption"></div>
</div>
          </div>
        </div>
