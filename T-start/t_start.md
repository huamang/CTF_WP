# 第一关

在视频中看到了ip地址

```
http://175.178.148.197:5000
```

![image-20220420142352117](https://tuchuang.huamang.xyz/img/image-20220420142352117.png)

进去以后是一个登录界面，需要输入手机号，认证验证码登录，但是发了收不到，猜测是不是已经开始考核了，直接抓包分析了一下

![image-20220420142522823](https://tuchuang.huamang.xyz/img/image-20220420142522823.png)

果然是有逻辑漏洞的

![image-20220420142620210](https://tuchuang.huamang.xyz/img/image-20220420142620210.png)

成功登录进去

![image-20220420142741533](https://tuchuang.huamang.xyz/img/image-20220420142741533.png)

![image-20220420145313817](https://tuchuang.huamang.xyz/img/image-20220420145313817.png)

```
AKIDThY5LKw4sJ051awKdJ4Xiu3yczGVF9X7
OaSEenfF2YpolHdjs00W6G4bqo6IGyeB
sec-1258666666
```

# 第二关

# 第三关

下载出来是一个流量包，追踪里面的tcp流，看到有个这个东西，`ANDROID BACKUP`

![image-20220421165554729](https://tuchuang.huamang.xyz/img/image-20220421165554729.png)

谷歌搜了一下，是安卓的备份文件，找到了[介绍](https://nelenkov.blogspot.com/2012/06/unpacking-android-backups.html)与[解密的文章](https://floatingoctothorpe.uk/2017/decrypting-android-backups-with-python.html)，利用提供的[python脚本](https://github.com/FloatingOctothorpe/dump_android_backup)

但是这里是利用了密码加密的，我们还需要找到密码，追踪tcp流找到了密码：`yun202203`

![image-20220421165935218](https://tuchuang.huamang.xyz/img/image-20220421165935218.png)

然后利用脚本解密，成功拿到压缩包

![image-20220421170014223](https://tuchuang.huamang.xyz/img/image-20220421170014223.png)

里面有一个apk文件，和一个flag.zip以及一个rsa加密相关的文件，zip文件加密了

![image-20220421170116864](https://tuchuang.huamang.xyz/img/image-20220421170116864.png)

利用key.en充当密文，pem文件充当密钥，解开得到密码

```
9BlteBJnZpwrRjbL0DsGlFz5M+MDG74jYIj0zzivGPVW75jYZQpdzpfrpEBcXAJqHrlZlEw9hMhRQ8FijkATyMxpKsPXEWT5K6M5
```

用这个密码解开压缩包，里面的内容是

```
1111111010010101010110111111110000010100011000111101000001101110100110100101000010111011011101010000110001000101110110111010110100100101101011101100000100100111011001010000011111111010101010101010111111100000000001111001000000000000110011010000110101010011011110001110110101101011000011110000110100101110010010111111000100001101000011010000100000100100101101001011010010101111010011110100111101001110000011110100101101001011010010001110011101101111011001110000001001010101001100011001001001001110111111111111011101101001010111001101110111001001110111000001111100011001000010100110110011111011010100111110010001100100011110011001100000000001001010101101010111001111111010110001101000101001010000011100111110010001000100101110101010011111111110111101011101000000010000000000111010111011110001010110111001110100000101110011101101110011101111111
```

长度为29的平方，尝试转为二维码

```
import PIL
from PIL import Image
MAX = 29

img = Image.new("RGB",(MAX,MAX))
str="1111111010010101010110111111110000010100011000111101000001101110100110100101000010111011011101010000110001000101110110111010110100100101101011101100000100100111011001010000011111111010101010101010111111100000000001111001000000000000110011010000110101010011011110001110110101101011000011110000110100101110010010111111000100001101000011010000100000100100101101001011010010101111010011110100111101001110000011110100101101001011010010001110011101101111011001110000001001010101001100011001001001001110111111111111011101101001010111001101110111001001110111000001111100011001000010100110110011111011010100111110010001100100011110011001100000000001001010101101010111001111111010110001101000101001010000011100111110010001000100101110101010011111111110111101011101000000010000000000111010111011110001010110111001110100000101110011101101110011101111111"
i = 0
for y in range (0,MAX):
    for x in range (0,MAX):
        if(str[i] == '1'):
            img.putpixel([x,y],(0, 0, 0))
        else:
            img.putpixel([x,y],(255,255,255))
        i = i+1
img.show()
img.save("flag.png")
```

![image-20220422234059886](https://tuchuang.huamang.xyz/img/image-20220422234059886.png)

结果为：/033yia8rqea1921ca61/systemlockdown

apk文件实验jadx反编译

![](https://tuchuang.huamang.xyz/img/image-20220422234926963.png)

拿到一个地址：175.178.148.197:80

访问：175.178.148.197/033yia8rqea1921ca61/systemlockdown，下载到了一个文件

![image-20220423000040928](https://tuchuang.huamang.xyz/img/image-20220423000040928.png)

查看一下readme

![image-20220423010124525](https://tuchuang.huamang.xyz/img/image-20220423010124525.png)

```c++
门禁用的是Windows 10，x86系统……经过一番分析，你成功拿到了门禁系统源码，可喜的是，门禁认证系统已经写死，即使是管理员也无法更新。
但，就在破解源码的过程中，管理员也觉察到门禁源码泄露，提前关闭了门禁系统，你输入的密码将无法认证。
时间一分一秒过去，不能再犹豫了，需要立即输入密码，解锁门禁。

PS: MSVC 2015以后的版本编译，Debug，不开启任何优化，请以提供的附加材料为准（Binary与下列源码表现一致，输入的答案通过与否请以该Binary的输出为准）。
flag：如果你认为输入12345可以解锁门禁，则请提交答案：md5(12345)


#include <iostream>

struct door_key {
        unsigned char passed : 1;
        unsigned char checksum1 : 2;
        unsigned char checksum2 : 2;
        unsigned char checksum3 : 3; 
};

//The system doesn't allow ANYBODY to log in now.
#define SYSTEM_SHUTDOWN 1

void check(char* password, door_key* d) {
        if (SYSTEM_SHUTDOWN) {
                return;
        }

        if (memcmp(password, "888888", 6) == 0) {
                d->passed = 1;
                d->checksum1 = 88;
                d->checksum2 = 88;
                d->checksum3 = 88;
        }
}

void call_the_police() {
        abort();
}

int main()
{
        door_key* checker1 = 0; 

        struct { 
                char password[6];
                char key_data;
        } management = { 0 };

        char ch = 0;
        char last_ch = 0;
        int i = 0;

        if (SYSTEM_SHUTDOWN) {
                std::cout << "Notify from the administrator: NOBODY is allowed to login now!!!" << std::endl;
                std::cout << "YOUR LOGIN REQUEST WILL NOT BE HANDLED AND WE WILL CALL THE POLICE INSTANTNLY IF YOU DIDN'T PASS THE CHECK." << std::endl;
        }
retry:
        std::cout << "Please enter your 6-digit password, type '[6 digit number] then Enter' to confirm (For example: 123456): " << std::endl;
        for (i = 0; i <= 6; i++) {
                ch = std::cin.get();

                if (ch == '\n')
                        break;
                if (!isdigit(ch) && ch != '\n')
                        call_the_police();

                // Developer A:
                //  Add an easy check, our strong 6-digit password is 888888 ! 
                //  Pre-check if every digit is the same. 
                if (ch != '\n' && last_ch && ch != last_ch) 
                        call_the_police();

                last_ch = ch;
                management.password[i] = ch;
        }; 

        checker1 = (door_key *)&(management.key_data); 

        check(management.password, checker1);

        if ((checker1->passed && (checker1->checksum1 == checker1->checksum2) && checker1->checksum3 > 0)) {
                std::cout << "Congurations! You have entered the correct password.";
        }
        else
                call_the_police();
}

```





# 第四关

里面有两个文件，第一个是一个缺了一个定位点的二维码，一个是一个压缩包

修复好二维码以后，扫描出结果，是一个zip文件

```
https://public.huoxian.cn/ctf/call_me.zip
```

![image-20220420170520228](https://tuchuang.huamang.xyz/img/image-20220420170520228.png)

再看题目的zip文件，直接打开报错，010看一下，文件头错了，修改成504B

![image-20220420170653476](https://tuchuang.huamang.xyz/img/image-20220420170653476.png)

打开里面是一个mp3文件，利用Audacity分析，发现是摩斯电码

![image-20220420170733781](https://tuchuang.huamang.xyz/img/image-20220420170733781.png)

手动识别一下

```
.---- ----. ----. .---- ----- ...-- ---.. -.... --... ----. --...
```

解码成19910386797

![image-20220420171104003](https://tuchuang.huamang.xyz/img/image-20220420171104003.png)

打开call_me.zip

![image-20220420171203670](https://tuchuang.huamang.xyz/img/image-20220420171203670.png)

```
https://darknet.hacker5t2ohub.com/
```

# 第六题

010分析一下，发现是docx，修改后缀打开

![image-20220420171704871](https://tuchuang.huamang.xyz/img/image-20220420171704871.png)

```
喜欢我给你的惊喜吗？
我已将线索藏到三个不同的地方，
其中一个提示为123456
来找我吧，
记住，你只能一个人来
否则，你会受到惩罚哦
```

设置视图中打开隐藏文字，找到了flag2

![image-20220420172816471](https://tuchuang.huamang.xyz/img/image-20220420172816471.png)

```
Flag2:772e91/webs
```

下面还有个图片

![image-20220420180526451](https://tuchuang.huamang.xyz/img/image-20220420180526451.png)

document.xml打开有这么一段字

![image-20220420185655034](https://tuchuang.huamang.xyz/img/image-20220420185655034.png)