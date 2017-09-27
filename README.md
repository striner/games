/*贪吃蛇小游戏*/

建立头文件"head.h":

int ArrSnake[20][3] = {0};

//蛇坐标最大值 蛇长
int g_SnakeL = 2;

//蛇的方向
int snakeDir = to_west;  

bool hadFood = true;

int g_mx, g_my;  //食物坐标
int g_score;  //得分

--------分隔符----------
建立主程序:

#include <stdio.h>
#include <stdlib.h>
#include <windows.h>
#include <conio.h>
#include <time.h>
#include "head.h"

#include <MMSystem.h>
#pragma comment(lib, "winmm.lib") //添加库

void MyPlaySound();  //音乐播放
void FirstPage();  //首页显示
void TestSpace();  //空格跳转
void StopMusic();  //停止播放音乐
void EnterPage();  //进入游戏
void BackGround();  //背景
void SetSnakeRandPos();
void DrawSnake();  //画蛇
void SnakeMove();  //蛇动
void DropSnake();  //销毁
void ChangeDir();  //改变方向
bool IsSnakeDie(); //死亡判断
void ProduceFood(); //随机产生食物
void SnakeGrow();  //蛇的成长
void Score();  //分数显示

int main(void)
{
	g_score = 0;
	MyPlaySound();  //音乐播放
	FirstPage();  //首页显示
	TestSpace();  //空格跳转
	StopMusic();  //停止播放音乐
	EnterPage();  //游戏等待

	system("cls");
	SetSnakeRandPos();  //为蛇产生一个随机的位置
	BackGround();
	

	//开始游戏
		while(1)
	{
		system("cls");

		if (IsSnakeDie() == false)
		{
			printf("\n YOU DIE !!!\n\n\n\n\n");
			break;
		}

		ProduceFood();  //为蛇产生随机食物
		ChangeDir();  //改变方向	 
		SnakeMove();  //蛇动
		SnakeGrow();  //蛇变长
		BackGround(); //显示背景
		Score();  //分数显示
		Sleep(1000); //1s
	}

	system("pause");
	return 0;
}

void FirstPage() //首页显示
{
	printf("\n\n\n\n\n\n");
	printf("\n\t\t\t    *<欢迎进入贪吃蛇世界>*\n");
	printf("\n\t\t\t  * * *<空格键开始游戏>* * *\n");  
	printf("\n\t\t       <W S A D 和 ↑ ↓ ← → 控制方向>\n\n");
	printf("\n\n\n\n\t\t\t\t\t\t\t** @striner **");
}
 
//音乐播放
void MyPlaySound()
{
	PlaySound(TEXT("backmusic.wav"), NULL, SND_FILENAME | SND_ASYNC /*| SND_LOOP*/ ); //资源连接
	//PlaySound(MAKEINTRESOURCE(IDR_WAVE1), NULL, SND_RESOURCE | SND_ASYNC  ); //资源添加至程序中 
}

void TestSpace() //空格键开始游戏
{
	char chInput;

	while(1)
	{
		chInput = _getch(); //无需回车自动检测 getch2005版后被弃用
		if (chInput == ' ' )  //输入检测
		{
			break;
		}
	}
}

void StopMusic() //音乐停止播放
{
	PlaySound(NULL, 0, 0);
}

void BackGround()
{
	int i = 0;
	
	for (i = 0; i < 20; i++)
	{
		printf(backGround[i]);
	}
}

void EnterPage() //进入游戏界面
{
	system("cls");
	printf("\n\n\n\n\n\n\n\n\n\n\t\t\t  开始游戏   ..\n\n\n\n\n\n\n\n");
	system("cls");
	printf("\n\n\n\n\n\n\n\n\n\n\t\t\t  开始游戏     ..\n\n\n\n\n\n\n\n");
	system("cls");
	printf("\n\n\n\n\n\n\n\n\n\n\t\t\t  开始游戏       ..\n\n\n\n\n\n\n\n");
	system("cls");
	printf("\n\n\n\n\n\n\n\n\n\n\t\t\t  开始游戏         ..\n\n\n\n\n\n\n\n");
	Sleep(100); 
}

void SetSnakeRandPos() //蛇随机出现的位置设置 
{
	int nx = -1;
	int ny = -1;

	//产生随机数
	srand((unsigned int)time(NULL)); //种种子 基础点
	nx = rand()%19 + 1;  //1~19
	ny = rand()%18 + 1;  //1~18

	//初始化蛇的三个节点
	ArrSnake[0][0] = ny; //行
	ArrSnake[0][1] = nx*2; //列
	ArrSnake[0][2] = to_west;

	ArrSnake[1][0] = ny; 
	ArrSnake[1][1] = nx*2 + 2; 
	ArrSnake[1][2] = to_west;

	ArrSnake[2][0] = ny;
	ArrSnake[2][1] = nx*2 + 4; 
	ArrSnake[2][2] = to_west;

	DrawSnake(); //画蛇
}

void DropSnake()
{
	int i = 0;
	for (i = 0; ArrSnake[i][0] != 0; i++)
	{
		strncpy(&backGround[ArrSnake[i][0]][ArrSnake[i][1]], "  ", 2);
	}
}

void DrawSnake()  //画蛇
{
	int i = 0;
	//SnakeGrow();
	for (i = 0; ArrSnake[i][0] != 0; i++)
	{
		strncpy(&backGround[ArrSnake[i][0]][ArrSnake[i][1]], "█", 2);
	}
}

void ChangeDir()  //方向控制
{ 
	//short两字节 高字节,低字节--触则非0
	if (GetAsyncKeyState(VK_UP) | GetAsyncKeyState('W'))   //同步检测 getch():同步检测  字母只检测大写 
	{
		if (ArrSnake[0][2] != to_south)
		{
			snakeDir = to_north;
		}
	}
	else if (GetAsyncKeyState(VK_DOWN) | GetAsyncKeyState('S'))
	{
		if (ArrSnake[0][2] != to_north)
		{
			snakeDir = to_south;
		}
	}
	else if (GetAsyncKeyState(VK_LEFT) | GetAsyncKeyState('A'))
	{
		if (ArrSnake[0][2] != to_east)
		{
			snakeDir = to_west;
		}
	}
	else if (GetAsyncKeyState(VK_RIGHT) | GetAsyncKeyState('D'))
	{
		if (ArrSnake[0][2] != to_west)
		{
			snakeDir = to_east;
		}
	}
}

void SnakeMove()  //蛇动
{
	int i = DEF_Snake_Long - 1;

	DropSnake(); //蛇的消毁
	for (i; i>=1; i--)
	{
		if (ArrSnake[i][1] == 0) //过滤
		{
			continue;
		}
		//后节点前移
		ArrSnake[i][0] = ArrSnake[i-1][0];
		ArrSnake[i][1] = ArrSnake[i-1][1];
		ArrSnake[i][2] = ArrSnake[i-1][2];
	}

	//处理蛇头
	ArrSnake[0][2] = snakeDir;

	if (ArrSnake[0][2] == to_west || ArrSnake[0][2] == to_east)
	{
		ArrSnake[0][1] +=  ArrSnake[0][2];
	}
	else
	{
		ArrSnake[0][0] +=  ArrSnake[0][2];
	}

	DrawSnake(); //画蛇
}

bool IsSnakeDie() //蛇的死亡判断
{
	ChangeDir();  //改变方向
	if (ArrSnake[0][2] == to_west || ArrSnake[0][2] == to_east)
	{
		//蛇头下一步,方块则死亡
		if (0 == strncmp(&backGround[ArrSnake[0][0]][ArrSnake[0][1] +  ArrSnake[0][2]], "█", 2) )
		{
			return false;
		}
	}
	else
	{
		if (0 == strncmp(&backGround[ArrSnake[0][0] +  ArrSnake[0][2]][ArrSnake[0][1]], "█", 2))
		{
			return false;
		}
	}

	return true;
}

void ProduceFood() //随机产生食物
{
	if (hadFood == false)  //判断是否产生新的食物
	{
		return;
	}

	//产生随机坐标
	srand((unsigned int)time(NULL));

	while(1)
	{
		bool bFlag = true;

		g_mx = rand()%18 + 1;   //1~18 行
		g_my = rand()%20 + 1;   //1~20 列

		for (int i = 0;ArrSnake[i][0] != 0; i++)  //排除蛇身与出现食物重合情况
		{
			if (g_mx == ArrSnake[i][0] && g_my == ArrSnake[i][1])
			{
				bFlag = false;
				break;
			}
		}
		if (bFlag == true) 
		{
			break;
		}
	}
	
	//坐标面食物
	strncpy(&backGround[g_mx][g_my*2], "★", 2);

	hadFood = false;
}

void SnakeGrow()   //蛇变长
{
	//蛇头坐标与食物坐标相同,蛇长大
	if (g_mx == ArrSnake[0][0] && g_my*2 ==  ArrSnake[0][1])
	{
		if (ArrSnake[g_SnakeL][2] == to_east)
		{
			ArrSnake[g_SnakeL+1][0] = ArrSnake[g_SnakeL+1][0];
			ArrSnake[g_SnakeL+1][1] = ArrSnake[g_SnakeL+1][1] - 2;
			ArrSnake[g_SnakeL+1][2] = ArrSnake[g_SnakeL+1][2];
		}
		else if (ArrSnake[g_SnakeL][2] == to_west)
		{
			ArrSnake[g_SnakeL+1][0] = ArrSnake[g_SnakeL+1][0];
			ArrSnake[g_SnakeL+1][1] = ArrSnake[g_SnakeL+1][1] + 2;
			ArrSnake[g_SnakeL+1][2] = ArrSnake[g_SnakeL+1][2];
		}
		else if (ArrSnake[g_SnakeL][2] == to_north)
		{
			ArrSnake[g_SnakeL+1][0] = ArrSnake[g_SnakeL+1][0] + 1;
			ArrSnake[g_SnakeL+1][1] = ArrSnake[g_SnakeL+1][1];
			ArrSnake[g_SnakeL+1][2] = ArrSnake[g_SnakeL+1][2];
		}
		else if (ArrSnake[g_SnakeL][2] == to_south)
		{
			ArrSnake[g_SnakeL+1][0] = ArrSnake[g_SnakeL+1][0] - 1;
			ArrSnake[g_SnakeL+1][1] = ArrSnake[g_SnakeL+1][1];
			ArrSnake[g_SnakeL+1][2] = ArrSnake[g_SnakeL+1][2];
    	}
		hadFood = true;
		g_score++;
		g_SnakeL++;
	}
}

void Score()  //分数显示
{
	//句柄 光标位置 分数显示处
	COORD rd;  //输出坐标
    rd.X = 55;  //对应第55列
    rd.Y = 8;  //对应第8行
    //COORD cursorPosition = {rd.X, rd.Y};
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), rd); //设置控制台光标位置
	//显示分数
	printf("得分: %d", g_score);
	if (g_score == 375)
	{
		printf("\n SO STRONG YOU ARE, YOU WIN !!!");
	}
}

-----分隔符-----

写在最后:

这个程序还有几个待解决问题:
1.0xC0000005: 写入位置 0x001C9FD0 时发生访问冲突。 运行异常
2.蛇增长偶尔异常
3.即将碰到墙时方向控制失效直接死亡
4.频闪

第一次用GitHub发表代码,好多功能都不清楚,大家别见笑哈~ 
这个贪吃蛇小游戏我是用vs去编写的,背景音乐名为backmusic(音乐名是自己自定义的),大家也可以下载自己喜欢的音乐,把音乐名称改成backmusic.wav与.exe文件放在一起也可以播放,注意音乐格式一定要是wav格式的才行.

