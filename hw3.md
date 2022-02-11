# ICCAD Assignment#3

## Problem1

第一题的代码如下

```bash
#!/bin/bash
echo "输入1～6,q"
t=0
while read ip 
do
if [[ $ip -eq 1 ]]; then
        echo "Hello World!"
        t=`expr $t + 1`

        echo "$t times print"
elif [[ $ip -eq 2 ]]; then
        ls -alR /home > a.txt
c=`grep -c '' a.txt`
awk '{ if ($9 ~ /^\..*/){printf("%s %s %s %s %s %s %s %s ",$1,$2,$3,$4,$5,$6,$7,$8) ; print "\033[1;31m" $9 "\033[0m" } else {print($1,$2,$3,$4,$5,$6,$7,$8,$9) } }' a.txt

elif [[ $ip -eq 3 ]]; then
        echo "input a name "
        read wayname
        cd
        find -name $wayname -type d
        cd $(find -name $wayname -type d)
        find -type f -exec stat -c "%s %n" {} \; | sort -nr |head -1
elif [[ $ip -eq 4 ]] ; then
        echo "input a file name "
        read flname
        cd
        way=$(find /home -name $flname)
        if [ -x $way ]; then
                hexdump $way
        fi
elif [[ $ip -eq 5 ]]; then
        ftp -n -v  <<- EOF
        open alpha.gnu.org 
        user anonymous

        ls -t  list.txt 
        bye
        EOF
        filename=`cat list.txt | awk '{print $9}' | head -n 1`
        echo $filename
elif [[ $ip -eq 6 ]] ; then
        pstree -pA>6.txt
        max=$(awk -F "[+|\`]" '{print NF}' 6.txt | sort | sed -n '$p')
        awk -F "[+|\`]" '{if(NF =='"$max"' ){printf $NF; printf"\n"}}' 6.txt | awk -F "[()]" '{print $2}' > last.txt
        line=`sed -n '$=' last.txt`
        for (( i=1; i<=$line; i++))
        do
                a=$(sed -n "$i"p last.txt)
                pstree -ps $a
        done

elif [[ $ip != "q" ]]; then
        echo "error"
else
	break
fi
done
```

在运行代码的时候首先输出一行引导使用者输入1～6 q

输入1的时候将返回“Hello World!",并且多次输入会返回输出的次数，如下图所示

![p1.1](/home/hyg/Downloads/hw3/last/p1.1.png)

在代码中采用了一个变量t来统计，输出的次数，每利用echo输出一次就加1并且进行一个输出。

当输入2的时候，按照题目要求，将我电脑中home下的文件以长格式输出，并且将隐藏文件以红色显示出来，结果如下图所示

![p1.2](/home/hyg/Downloads/hw3/last/p1.2.png)

可以看到这里面以.开头的文件被标红了，在这一步的处理过程中，首先利用`ls -alR`的命令显示全部的文件（包括文件夹中的文件的详细信息并将其存到一个txt文件中，之后对这个文件进行文本处理，这里利用了awk命令，并结合正则表达式，以空格“ ”为分隔符选取第九个并判断是否以 . 开头，

```bash
awk '{ if ($9 ~ /^\..*/){printf("%s %s %s %s %s %s %s %s ",$1,$2,$3,$4,$5,$6,$7,$8) ; print "\033[1;31m" $9 "\033[0m" } else {print($1,$2,$3,$4,$5,$6,$7,$8,$9) } }' a.txt
```

若满足条件则利用printf将输出的颜色进行修改，并进行输出，不符合条件的则按照原格式进行输出。

若输入3，这时需要输入一个文件夹名称再找到其中的最大文件

![p1.3.png](/home/hyg/Downloads/hw3/last/p1.3.png)

在这一步中，首先利用find命令`find -name $wayname -type d`找到了该文件的绝对地址，并通过cd转移到该地址下，这时继续利用find的命令

```bash
find -type f -exec stat -c "%s %n" {} \; | sort -nr |head -1
```

查找该目录下的文件，并对其的大小进行排序，并选取最开头的文件进行输出。

若输入4，这时将会需要先输入一个文件名，如果说这个文件存在则且可执行，则会将其以十六进制的格式输出，如下图所示

![p1.4.png](/home/hyg/Downloads/hw3/last/p1.4.png)

在这部分代码当中通过find来查找这个文件，并且判断其是否为可执行文件，并利用hexdump进行输出

```bash
		way=$(find /home -name $flname)
        if [ -x $way ]; then
                hexdump $way
        fi
```

当输入5的时候，这时将访问alpha.gnu.org的ftp并且输出其中最新的项目，结果如图所示

![p1.5.png](/home/hyg/Downloads/hw3/last/p1.5.png)

这里我们首先open这个ftp并利用ls命令将其中的文件按照时间顺序输出到一个txt文件中，之后我们利用awk以及head等命令来获取其中最新的项目名称并进行输出

```bash
		ftp -n -v  <<- EOF
        open alpha.gnu.org 
        user anonymous

        ls -t  list.txt 
        bye
        EOF
        filename=`cat list.txt | awk '{print $9}' | head -n 1`
        echo $filename
```

当输入6的时候，显示电脑中pstree里最深的进程，如图所示

![p1.6.png](/home/hyg/Downloads/hw3/last/p1.6.png)

这里利用了pstree关于PID码来显示进程的功能，首先将其存进一个txt文件中，利用awk命令获得其最深的层数，再利用awk获得最深进程的PID码，再单独输出其全部的进程。

```bash
		pstree -pA>6.txt
        max=$(awk -F "[+|\`]" '{print NF}' 6.txt | sort | sed -n '$p')
        awk -F "[+|\`]" '{if(NF =='"$max"' ){printf $NF; printf"\n"}}' 6.txt | awk -F "[()]" '{print $2}' > last.txt
        line=`sed -n '$=' last.txt`
        for (( i=1; i<=$line; i++))
        do
                a=$(sed -n "$i"p last.txt)
                pstree -ps $a
        done
```

最后输入q则直接break出整个循环，如果输入了除1～6之外的字符，则输出error字样.

## Problem 2

通过`gcc -o demo_inout demo_inout.c`编译了demo_inout.c文件并生成了demo_inout这一个可执行文件，运行这个可执行文件之后输入以空格分隔的字符串，该可执行文件则将其分别输出给了两个不同的文件，通过查看该代码，可以发现，通过利用fscanf的按格式读入功能，来将输入分别存入了不同的变量，并进行了不同的输出。

![p2.png](/home/hyg/Downloads/hw3/last/p2.png)

## Problem 3

第三题中主要通过输入多个网址，并通过ping获得其的ip地址以及获取平均时间，获得的结果如下图所示

![p3.png](/home/hyg/Downloads/hw3/last/p3.png)

在这里通过读入的数据利用数组的功能，分别存到数组当中，利用ping函数，在ping5次之后得到的文本文件，利用awk截取其中关于该网址的ip地址以及平均响应时间。

```bash
#!/bin/bash
read allwz
ARR=($allwz)
count=${#ARR[*]}
i=0
y=1
echo "">p2_n.txt
while [ $i -lt $count ] 
do
        #echo ${ARR[$i]}
        ping -c 5 ${ARR[ $i ]}>1.txt
        sed -n '$p' 1.txt>any.txt
        time[$i]=`cut -d'/' -f5 any.txt`
        #echo ${time[$i]}
        sed -n 1p 1.txt>any.txt
        cut -d')' -f1 any.txt>any1.txt
        ip[$i]=`cut -d'(' -f2 any1.txt`
        #echo ${ip[$i]}
        if [ ! -n "${time[$i]}" ]; then
                echo "${ARR[$i]} @ ${ip[i]} is unreachable">>p2_n.txt
        elif [ $y -eq 1 ]; then
                echo "${ARR[$i]} @ ${ip[i]} @ ${time[i]} ms ">p2.txt
                y=0
        else
                echo "${ARR[$i]} @ ${ip[i]} @ ${time[i]} ms">>p2.txt
        fi
        #echo "${ARR[$i]} @ ${ip[i]} @ ${time[i]}">>p2.txt
        let i+=1
done
sort -n -k 5 -t " " p2.txt
sort -n -k 3 -t " " p2_n.txt
```

这里在获取信息的过程中，对数组time是否为空进行分类，分为可以ping通的，和ping不到的两个文件，并分别进行排序，之后进行一个输出。

## Problem 4

在这一题中涉及了需要从bing上下载其壁纸和程序定时运行两个部分，关于壁纸下载部分，利用了wget函数，首先我们通过手动下载该壁纸，通过分析它的下载路径在该网站html中的位置，这时通过awk文本处理文本处理工具，获取该链接，并再次利用wget工具下载这张图片，到这里下载壁纸这一步就已经完成了，代码如下

```bash
#!/bin/bash
wget -O bing.txt www.bing.com
ip=`awk -F "><" '{print $19}' bing.txt | sed -n 1p | awk -F "\"" '{printf("www.bing.com%s",$4)}'`
wget -P /home/hyg/Pictures $ip

```

第二步中设置定时运行，这里利用了`crontab -e`在其中添加了一条命令 

```bash
 0 12 * * * bash /home/hyg/Downloads/hw3/bing.sh
```

使其在每天中午12点定时下载一张bing上的壁纸，下面附上一张，写这个文件当天下载的壁纸。

![12.23.jpg](/home/hyg/Pictures/12.23.jpg)