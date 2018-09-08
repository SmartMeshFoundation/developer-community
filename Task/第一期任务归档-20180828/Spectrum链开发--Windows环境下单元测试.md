作者: 唐爽 校阅者: rectinajh
<div class="show-content-free">
            <blockquote>
<p>系统环境 win10</p>
<p>注：不同windows发布版本安装过程可能略有不同</p>
</blockquote><p>环境依赖:</p><p>Go, GCC</p><p>Go下载地址：&nbsp;https://golang.org/dl/</p><p>笔者下载的是1.10.2.&nbsp; &nbsp;64位版本:</p><p>完整下载地址: https://dl.google.com/go/go1.10.2.windows-amd64.msi</p><p>安装:</p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 600px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 77.72%;"></div>
<div class="image-view" data-width="772" data-height="600"><img  src="https://upload-images.jianshu.io/upload_images/3176928-d95c6c97e3e3db5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/772/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><p>笔者安装的路径为 C:\Go目录下. 添加&nbsp; &nbsp;C:\Go\bin到环境变量中.</p><blockquote>
<p>任意目录下执行go version,显示版本号则安装配置成功.</p>
<p>C:\Users\tangshua&gt;go version</p>
<p>go version go1.10.2 windows/amd64</p>
</blockquote><p>MinGw下载地址 :&nbsp;https://sourceforge.net/projects/mingw-w64/</p><p>笔者下载的GW完整下载地址：&nbsp;https://jaist.dl.sourceforge.net/project/mingw-w64/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe</p><p>安装MinGW:</p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 578px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 73.26%;"></div>
<div class="image-view" data-width="789" data-height="578"><img  src="https://upload-images.jianshu.io/upload_images/3176928-9089bfb3ea9f9068.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/789/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><p>笔者安装在D:\mingw-w64,安装完成后如下. 添加D:\mingw-w64\bin到环境变量中.</p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 590px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 84.41%;"></div>
<div class="image-view" data-width="1283" data-height="1083"><img  src="https://upload-images.jianshu.io/upload_images/3176928-33972707a6d0745d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><blockquote><p>Spectrum安装</p></blockquote><p>下载源文件</p><p>https://github.com/SmartMeshFoundation/Spectrum</p><p>笔者放在了D盘.切换到tests目录.&nbsp; 执行Go test -v</p><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 520px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 74.4%;"></div>
<div class="image-view" data-width="1082" data-height="805"><img  src="https://upload-images.jianshu.io/upload_images/3176928-4c5f70dda68f86ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><div class="image-package">
<div class="image-container" style="max-width: 684px; max-height: 669px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 97.81%;"></div>
<div class="image-view" data-width="684" data-height="669"><img  src="https://upload-images.jianshu.io/upload_images/3176928-d371bdc4da236bcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/684/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><blockquote>
<p>其它点:</p>
<p>执行报错解决办法:</p>
<p>1.&nbsp; &nbsp;将代码放在Go目录下.</p>
<p>C:\Go\src\github.com\SmartMeshFoundation\Spectrum</p>
<p>笔者在block_test_util.go代码中发现如下定义,可能导致相应类库无法找到而报错.</p>
<p>import (
   "bytes"
   "encoding/hex"
   "encoding/json"
   "fmt"
   "math/big"

   "github.com/SmartMeshFoundation/Spectrum/common"
   "github.com/SmartMeshFoundation/Spectrum/common/hexutil"
   "github.com/SmartMeshFoundation/Spectrum/common/math"
   "github.com/SmartMeshFoundation/Spectrum/consensus/ethash"
   "github.com/SmartMeshFoundation/Spectrum/core"
   "github.com/SmartMeshFoundation/Spectrum/core/state"
   "github.com/SmartMeshFoundation/Spectrum/core/types"
   "github.com/SmartMeshFoundation/Spectrum/core/vm"
   "github.com/SmartMeshFoundation/Spectrum/ethdb"
   "github.com/SmartMeshFoundation/Spectrum/params"
   "github.com/SmartMeshFoundation/Spectrum/rlp"
)
</p>
<p>2.&nbsp; &nbsp;在tests目录下创建testdata目录，同时在testdata目录下分别创建</p>
<p>BlockchainTests，GeneralStateTests,TransactionTests,VMTests,RLPTests,BasicTests目录。</p>
<p>笔者在init_test.go 文件中发现如下定义:</p>
<p>var (
   baseDir            = filepath.Join(".", "testdata")
   blockTestDir       = filepath.Join(baseDir, "BlockchainTests")
   stateTestDir       = filepath.Join(baseDir, "GeneralStateTests")
   transactionTestDir = filepath.Join(baseDir, "TransactionTests")
   vmTestDir          = filepath.Join(baseDir, "VMTests")
   rlpTestDir         = filepath.Join(baseDir, "RLPTests")
   difficultyTestDir  = filepath.Join(baseDir, "BasicTests")
)</p>
<p>但是并未在文件夹中发现，因此手动创建了改文件夹.</p>
<p><br></p>
</blockquote><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 365px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 52.18000000000001%;"></div>
<div class="image-view" data-width="1468" data-height="766"><img  src="https://upload-images.jianshu.io/upload_images/3176928-cc7569805bf7a2dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></div>
</div>
<div class="image-caption"></div>
</div><p><br></p><div class="image-package">
<div class="image-container" style="max-width: 700px; max-height: 530px; background-color: transparent;">
<div class="image-container-fill" style="padding-bottom: 41.8%;"></div>
<div class="image-view" data-width="1268" data-height="530"><img src="https://upload-images.jianshu.io/upload_images/3176928-73465f59ad0804d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp"></div>
</div>
<div class="image-caption"></div>
</div>
          </div>
