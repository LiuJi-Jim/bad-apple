Bad Apple
===

Bad Apple is a popular comic animate.
Many friends created their own editions with different tools such as *Excel*, *cmd*, *Mine Sweeper*, and even some other interesting things.
This is the JavaScript edition using canvas.

Better **NOT** open *movie480.js* with a text editor! It's a huge file (39MB) and contains only **ONE** line.

Google Chrome is the best browser to view.

===

Bad Apple 是一个很流行的动画短片。
网上有很多人制作了不同的版本，如*Excel*版、*命令行*版、*扫雷版*，还有其他一堆有意思的东东。
这是JavaScript版，使用canvas。

最好**不要**用文本编辑器打开*movie480.js*！这个文件很大（39MB）而且只有**1**行。

使用Google Chrome浏览器可以获得最佳效果。

===

**数据来源**

YouT*be上下载的480*360视频，用kmplayer输出所有单帧。

**预处理**

用C#写个程序，按照如下步骤处理图片，生成需要的js文件。

把256色灰度图规范化到64色（为了方便，还省空间，对效果影响不大），这样1个字符可以表示1个像素点，这时候的“视频”是个字符串。

用LZW算法把字符串压缩，结果是二进制，用可见字符表示二进制。

BASE64可以，但是体积浪费比较多，这里用了一种使用了多达91个可见字符来表示二进制的方法。

91*91=8281<8192也就是2<sup>13</sup>，所以可以用两个字符表示13位，而BASE64只能表示12位。

然后再加上一点基本的信息，比如视频的宽高，为了方便这些数据都直接被赋值到window上了。

**播放**

播放用canvas，Context2D提供getImageData和putImageData方法，这是个字节数组，可以直接按照RGBA访问当前帧，速度飞快。

由于“视频”文件很大(38MB)，又不想一开网页就卡死在这，所以用了个动态加载script标签(文件太大，这也是治标不治本)。

解压，先用那个“BASE91”逆向出“二进制”--其实就是字节数组，然后照常用LZW解压。

因为不是整字节压缩，而是13位一组，而且压缩算法本身就是要按pattern来压缩的，pattern长度不以，所以每次解压解出多少东西是不定的，于是乎每解压出足够一帧的数据，就把多余的作为下一帧的开始。

每解压一帧就播放一帧，播放的时候要定时，并且速度不够的时候要能跳帧。

这里的跳帧比较残酷，因为是即时解压的，所以即使跳帧也必须继续解压，只是不画画而已。

**其他**

IE9下看不到audio的控制条，不过声音倒是真的有

最早是用span做像素点的，只能播放120*90分辨率，性能惨不忍睹，无视吧。