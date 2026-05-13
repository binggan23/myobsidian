## 开始的开始
创建子项目，在创建的项目(不要选mvn，选默认的java，mvn后面学)
点击项目文件夹，new->module->创建子项目
```java
public class Main {  
    public static void main(String[] args) {  
        System.out.println("hello world");  
    }  
}
//TIP 
//最基本的代码
//public是公共属性，Main是文件名称，没错，这个只是文件名称
//static void main(String[] args){
//    System.out.println();
//}
//这些才是主函数
```




....此处省略100000字
```java
package wuziqi;

import java.applet.Applet;
import java.applet.AudioClip;
import java.net.URL;
import java.util.Scanner;

public class Gobang {
    //存储棋盘
    char[][] chessboard ={
            {'╔','╦','╦','╦','╦','╦','╦','╦','╦','╗'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╠','╬','╬','╬','╬','╬','╬','╬','╬','╣'},
            {'╚','╩','╩','╩','╩','╩','╩','╩','╩','╝'}
    };
    //分隔
    String sep = "════";
    //棋子个数
    int totalPosition = chessboard.length*chessboard[0].length;
    //棋子类型
    char pieceA = '◎';
    char pieceB = '◉';

    //固定格式
    public void showChessboard(){
        System.out.println("    0    1    2    3    4    5    6    7    8    9");
        for (int i = 0; i < chessboard.length; i++) {
            System.out.print(i+"   ");
            for (int j = 0; j < chessboard[i].length; j++) {
                if(j == chessboard[i].length-1){
                    System.out.print(chessboard[i][j]);
                }
                else{
                    System.out.print(chessboard[i][j] + sep);
                }
            }
            System.out.println();
            if(i<chessboard.length-1){
                System.out.println("    ║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
            }
        }
    }

    //输入
    public void scannerChessboard(){
        Scanner sc = new Scanner(System.in);
        inputer:
        for(int times = 0; times < totalPosition; times++){
            char tmpPiece =  (times % 2 == 0 ? pieceA : pieceB);
            showChessboard();
            System.out.println("请" + (times % 2 == 0 ? "A" : "B") + "落子：");
            while(true){
                if(sc.hasNextInt()) {
                    int position = sc.nextInt();
                    if(position >= 0 && position < totalPosition){
                        int row = position / chessboard.length;//行
                        int col = position % chessboard[0].length;//列
                        if(chessboard[row][col] == pieceA || chessboard[row][col] == pieceB){
                            //位置占用错误
                            System.out.println("错误[003]重新落子");
                            playAudio("illegal.wav");
                        }
                        else{
                            chessboard[row][col] = tmpPiece;
                            playAudio("fill.wav");
                            if(isWin(tmpPiece,times)){
                                break inputer;
                            }
                            break;
                        }
                    }
                    else {
                        //位置地址错误
                        System.out.println("错误[000]重新落子");
                        playAudio("illegal.wav");
                    }
                }
                else{
                    //输入错误
                    System.out.println("错误[001]重新落子");
                    playAudio("illegal.wav");
                    sc.next();
                }
            }
        }
    }

    //iswin
    public boolean isWin(char tmpPiece,int times){
        //水平+垂直+斜向
        //char tmpPiece =  (times % 2 == 0 ? pieceA : pieceB);
        for(int i = 0; i < chessboard.length; i++){
            for(int j = 0; j < chessboard[0].length; j++){
                //水平
                //(i,j)(i,j+1)(i,j+2)(i,j+3)(i,j+4)&&j<chessboard[0].length-4
                boolean flag01 = j < chessboard[0].length-4
                        && chessboard[i][j] == tmpPiece
                        && chessboard[i][j+1] == tmpPiece
                        && chessboard[i][j+2] == tmpPiece
                        && chessboard[i][j+3] == tmpPiece
                        && chessboard[i][j+4] == tmpPiece;
                //垂直
                //(i,j)(i+1,j)(i+2,j)(i+3,j)(i+4,j)&&i<chessboard.length-4
                boolean flag02 = i < chessboard.length-1
                        && chessboard[i][j] == tmpPiece
                        && chessboard[i+1][j] == tmpPiece
                        && chessboard[i+2][j] == tmpPiece
                        && chessboard[i+3][j] == tmpPiece
                        && chessboard[i+4][j] == tmpPiece;
                //135度角
                //(i,j)(i+1,j+1)(i+2,j+2)(i+3,j+3)(i+4,j+4)&&i<chessboard.length-4&&j<chessboard[0].length-4
                boolean flag03 = i < chessboard.length-4
                        &&j<chessboard[0].length-4
                        && chessboard[i][j] == tmpPiece
                        && chessboard[i+1][j+1] == tmpPiece
                        && chessboard[i+2][j+2] == tmpPiece
                        && chessboard[i+3][j+3] == tmpPiece
                        && chessboard[i+4][j+4] == tmpPiece;
                //45度角
                //(i,j)(i+1,j-1)(i+2,j-2)(i+3,j-3)(i+4,j-4)&&i>4&&j<chessboard[0].length-4
                boolean flag04 = i > 4
                        &&j<chessboard[0].length-4
                        && chessboard[i][j] == tmpPiece
                        && chessboard[i+1][j-1] == tmpPiece
                        && chessboard[i+2][j-2] == tmpPiece
                        && chessboard[i+3][j-3] == tmpPiece
                        && chessboard[i+4][j-4] == tmpPiece;
                if(flag01 || flag02 || flag03 || flag04){
                    showChessboard();
                    System.out.println((times % 2 == 0 ? "A" : "B")+"胜");
                    playAudio("win.wav");
                    return true;
                }
            }
        }
        return false;
    }

    //播放音乐
    public void playAudio(String path){
        URL url = Gobang.class.getResource(path);
        AudioClip clip = Applet.newAudioClip(url);
        clip.play();
        try {
            Thread.sleep(500L);
        } catch (InterruptedException e) {}
    }
}




//        System.out.println("╔════╦════╦════╦════╦════╦════╦════╦════╦════╗");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╠════╬════╬════╬════╬════╬════╬════╬════╬════╣");
//        System.out.println("║    ║    ║    ║    ║    ║    ║    ║    ║    ║");
//        System.out.println("╚════╩════╩════╩════╩════╩════╩════╩════╩════╝");

```



以上是一个小项目，下面才是真正的爪哇
# 类
## 定义
class没得说
class 类型 名词{}
类型有public 公开
	protected 保护的，默认的，只能在自己内部和同一个包内使用
	private 私有的
有个东西叫做构造函数
就是初始化
public 类名(内容){内容}
java没有析构，因为java有自动释放





# 接口
## 定义
一种限制的类，这种类不能实现具体，没有构造函数
只能是public