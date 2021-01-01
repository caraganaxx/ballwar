# #include<stdio.h>
#include<graphics.h>

//游戏画面尺寸
#define High 480 
#define Width 640
//砖块个数 
#define Brick_num 10

//全局变量
//打开背景音乐
MUSIC music;	
//小球的坐标
int ball_x,ball_y; 
//小球的速度
int ball_vx,ball_vy; 
//挡板的半径大小
int radius; 
//挡板的中心坐标
int bar_x,bar_y; 
//挡板的高度和宽度 
int bar_high,bar_width;
//挡板上下左右位置坐标 
int bar_left,bar_right,bar_top,bar_bottom;
//每个砖块是否存在，存在为1，不存在为0 
int isBrickExisted[Brick_num];
//砖块的高度和宽度 
int brick_high,brick_width;
//随机砖块位置 
int block_x,block_y;
//随机砖块速度 
int block_vx,block_vy;
//累计得分变量 
int score; 
//控制结束变量 
int terminator;
int terminator2;
int tdyam=1;
int con=1;
int *pcon=&con;


void startup();
void gamestart();
void clean();
void show();
void updateWithoutInput(int *p);
void updateWithInput();
void cleana();
void showa();
void updateWithoutInputa(int *p);
void gameover();
void circulation(int *p);
void again();
void flag1(int *p);
void flag2(int *p);
 
int main()
{
	music.OpenFile("theonestudio (V0).mp3");
	music.Play();
	if (music.GetPlayStatus() == MUSIC_MODE_STOP )
	{
		//从头开始播放
		music.Play(0);
	}
	
	//判断重新开始 
	while(con)
	{
		startup(); 
		gamestart();
		
		int count=0;
		key_msg keyMsga = {0};
		for (;count<1; delay_fps(10)) 
		{
			//按键消息处理
			if(kbmsg()) 
			{
				keyMsga = getkey();
				if (keyMsga.key =='1' )
				{
					count++;
					flag1(pcon);
				}
			    if(keyMsga.key=='5')
				{
					count++;
					flag2(pcon);
				}
			}
		}
	} 
	//关闭画面 
	closegraph();
	//关闭背景音乐 
	music.Close();
	
	return 0;
	
}
 
void flag1(int *p)
{
	terminator2=1;
	block_x=random(128000)%Width;
	block_y=random(72000)%High;
	while(terminator2)
	{
		int block_left=block_x-32;
		int block_right=block_x+32;
		int block_top=block_y-24;
		int block_bottom=block_y+24;
		//把之前绘制的内容取消 
		cleana();
		//随机关卡与用户输入无关的更新 
		updateWithoutInputa(p);
		//与用户输入有关的更新
		updateWithInput();
		//显示新画面 
		showa();
	}
	//清屏
	//cleardevice();
	//游戏结束，进行后续处理
	gameover();	
	again();
	circulation(p); 
	
	Sleep(5);
}
 
void flag2(int *p)
{
	//判断游戏是否继续 
	terminator=1;
	//游戏循环执行 
	while(terminator)
	{
		clean();
		//固定关卡与用户输入无关的更新 
		updateWithoutInput(p);
		updateWithInput();
		show(); 
	}
//	cleardevice(); 
	gameover();	
	again();
	circulation(p); 
	
	Sleep(5);	
}

//数据初始化
void startup() 
{
	ball_x =Width/2;
	ball_y =High/2;
	ball_vx=1;
	ball_vy=1; 
	radius=20;
	
	bar_high=High/20;
	bar_width=Width/2;
	bar_x=Width/2;
	bar_y=High-bar_high/2;
	bar_left=bar_x-bar_width/2;
	bar_right=bar_x+bar_width/2;
	bar_top=bar_y-bar_high/2;
	bar_bottom=bar_y+bar_high/2;
	
	brick_width=Width/Brick_num;
	brick_high=High/Brick_num;
	
	score=0;
	terminator=1; 
	//固定关卡砖块设定 
	int i;
	for(i=0;i<Brick_num;i++)
	{ 
		isBrickExisted[i]=1;
	}
	//初始化界面 
	initgraph(Width,High);
	
}

//游戏开始，显示画面 
void gamestart()
{
	//创建图片对象
	PIMAGE picture1= newimage();
	//从文件中获取图像（和原图一样大小）
	//图片文件名
	getimage(picture1, "1.png");
	//绘制图像，(0, 0)是图片绘制区域的左上角		
	putimage(0, 0, picture1);
	setcaption("Happy New Year ! ! !") ;
	//先画一个矩形，以便于看出背景色的问题
	setfillcolor(EGERGB(0x0, 0x80, 0x80));
	bar(50, 50, 600, 200);
	//输出文字 
	//设置文字背景填充方式为透明，默认为OPAQUE不透明
	setbkmode(TRANSPARENT);
	//文字颜色
	setcolor(YELLOW);
	//设置字体，第一个参数是字体的高度（像素），第二个参数是字体的宽度，第二个参数如果为0，就使用默认比例值
	//如果高度为12，即相当于小五号字，或者9磅字
	setfont(80,0,"微软雅黑");
	outtextxy(80,50,"WELCOME！");
	outtextxy(60,120," 开 始 游 戏"); 
	//延时 
	Sleep(3);
	//销毁图像，释放内存(图像是动态分配的，注意不要内存泄漏)
	delimage(picture1);
	//用户输入 
	getch();
	cleardevice();

	PIMAGE picture11= newimage();
	getimage(picture11, "1.png");		
	putimage(0, 0, picture11);
	setcaption("Happy New Year ! ! !") ;
	setbkmode(TRANSPARENT);
	setcolor(BLUE);
	setfont(50,0,"微软雅黑");
	outtextxy(80,50,"游戏介绍：用户通过控制'w',");
	outtextxy(80,100,"'a','s','d'来移动挡板，实");
	outtextxy(80,150,"现小球反弹撞击砖块来得分"); 
	outtextxy(80,200,"按任意键进入游戏");
	
	Sleep(3);
	delimage(picture11);
	getch();
	cleardevice(); 
	
	PIMAGE picture12= newimage();
	getimage(picture12, "1.png");		
	putimage(0, 0, picture12);
	setcaption("Happy New Year ! ! !") ;
	setfillcolor(EGERGB(0x0, 0x80, 0x80));
	bar(50, 50, 500,300);
	setcolor(EGERGB(0x0, 0xFF, 0x0));
	//文字背景色（注意setbkcolor函数也会同时改变文字背景色）
	setfontbkcolor(EGERGB(0x80, 0x00, 0x80));
	setfont(60, 0, "楷体");
	outtextxy(80,100,"随机关卡~~~1");
	outtextxy(80,160,"固定关卡~~~5"); 
	outtextxy(80,220,"请选择——");
	delimage(picture12);
	getch();
	cleardevice();
	
	Sleep(3);
}

//固定关卡
//消除画面 
void clean()
{
	PIMAGE picture2= newimage();
	//从文件中获取图像，引入背景图片 
	getimage(picture2, "1.png");
	//重新绘制图像，(0, 0)是图片绘制区域的左上角		
	putimage(0, 0, picture2);
	//显示游戏界面 
	setcolor(BLACK);
	setfillcolor(BLACK);
	//绘制黑线黑色填充的圆
	fillellipse(ball_x,ball_y,radius,radius); 
	//绘制黑线黑色填充的挡板
	bar(bar_left,bar_top,bar_right,bar_bottom); 
	
	int i,brick_left,brick_right,brick_top,brick_bottom;
	for(i=0;i<Brick_num;i++)
	{
		brick_left=i*brick_width;
		brick_right=brick_left+brick_width;
		brick_top=0;
		brick_bottom=brick_high;
		//绘制黑色黑线填充的砖块 
		if(isBrickExisted[i])
		{ 
			setcolor(BLACK);
			setfillcolor(BLACK);
			bar(brick_left,brick_top,brick_right,brick_bottom);
		}
	}
	
	setbkmode(TRANSPARENT);
	delimage(picture2);
	
 } 
 
//显示游戏画面 
void show()
{	
	setcaption("ballwar ! ! !") ;
	
	setcolor(YELLOW);
	setfillcolor(GREEN);
	//黄线、绿色填充的圆 
	fillellipse(ball_x,ball_y,radius,radius);
	//黄线、绿色填充的挡板 
	bar(bar_left,bar_top,bar_right,bar_bottom);
	
	int i,brick_left,brick_right,brick_top,brick_bottom;
	for(i=0;i<Brick_num;i++)
	{
		brick_left=i*brick_width;
		brick_right=brick_left+brick_width;
		brick_top=0;
		brick_bottom=brick_high;
		//砖块存在，绘制砖块
		if(isBrickExisted[i]) 
		{
			setcolor(YELLOW);
			setfillcolor(BLUE);
			//绘制黄线蓝色填充的砖块 
			bar(brick_left,brick_top,brick_right,brick_bottom);
		}
	}
	//延时 
	Sleep(3);
	
}

//与用户输入无关的更新
void updateWithoutInput(int *p)
{
	//挡板和小球碰撞，小球反弹 
	if((ball_y+radius>=bar_top)&&(ball_y+radius<bar_bottom-(8*bar_high)/9))
	{
		if((ball_x>=bar_left)&&(ball_x<=bar_right))
		{ 
			ball_vy=-ball_vy;	
		} 
	}else if((ball_y-radius<=bar_bottom)&&(ball_y-radius>bar_top-(8*bar_high)/9))
	{
		if((ball_x>=bar_left)&&(ball_x<=bar_right))
		{ 
		ball_vy=-ball_vy;	
		} 
	}
	//如果小球竖直方向速度为0，重新设定一个新速度 
	if(ball_vy<=1)
		ball_vy=2;  
	//水平速度为0，重新设定 
	if(ball_vx<=1)
	 	ball_vx=2;
	//更新小球的坐标 
	ball_x=ball_x+ball_vx;
	ball_y=ball_y+ball_vy;
	
	//小球和边界碰撞改变方向 
	if((ball_x==radius)||(ball_x==Width-radius))
	{ 
		ball_vx=-ball_vx;
	} 
	if((ball_y<=radius))
	{ 
		ball_vy=-ball_vy;
	} 
	if(ball_y>=High-radius)
	{
		terminator=0; 
		*p=0;
	}
	
	//判断小球是否和某个砖块碰撞 
	int i,brick_left,brick_right,brick_bottom;
	for(i=0;i<Brick_num;i++)
	{
		if(isBrickExisted[i])//砖块存在才判断 
		{
			brick_left=i*brick_width;
			brick_right=brick_left+brick_width;
			brick_bottom=brick_high;
			if((ball_y<=brick_bottom+radius)&&(ball_x>=brick_left)&&(ball_x<=brick_right))
			{
				ball_vy=-ball_vy;
				//砖块消失 
				isBrickExisted[i]=0;
				ball_vy+=1;
				//响铃
				printf("\a");
				//得分加1 
				score++;				
			}
			if((ball_x>=brick_left-radius)&&(ball_x<brick_right-60)
			&&(ball_y<=brick_bottom+radius)&&(ball_y>=0))
			{
				ball_vx=-ball_vx;
				isBrickExisted[i]=0;
				ball_vy++;
				ball_vx++;
				printf("\a");
				score++;
			}else if((ball_x<=brick_right+radius)&&(ball_x>brick_left+60)
			&&(ball_y<=brick_bottom+radius)&&(ball_y>=0))
			{
				ball_vx=-ball_vx;
				isBrickExisted[i]=0;
				ball_vy++;
				ball_vx++;
				printf("\a");
				score++;
			}
		}
	}
	
	//重置砖块 
	int j=0,k,l,m,n;
	int NoBrick[Brick_num]={0};
	for(j=0;j<Brick_num;j++)
	{
		if(isBrickExisted[j]==NoBrick[j])
			k++;
	} 
	m=1;
	while(m) 
	{
		if(k==(m*10))
		{
			for(j=0;j<Brick_num;j++) 
				isBrickExisted[j]=1;
				m++;
		}
		break;
	}
	n=1;
	while(n)
	{
		if(score==(n*10))
		{
			ball_vy=2;
			n++;
		}
		break;		
	}
	
	Sleep(3);
}

//随机关卡 
void cleana()
{
	setcaption("ballwar ! ! !") ;
	PIMAGE picture21= newimage();
	getimage(picture21, "1.png");		
	putimage(0, 0, picture21);

	setcolor(BLACK);
	setfillcolor(BLACK);
	//绘制黑线、黑色填充的圆
	fillellipse(ball_x,ball_y,radius,radius); 
	//绘制黑线黑色填充的挡板
	bar(bar_left,bar_top,bar_right,bar_bottom); 
	
	int block_left=block_x-32;
	int block_right=block_x+32;
	int block_top=block_y-24;
	int block_bottom=block_y+24;
	setcolor(BLACK);
	setfillcolor(BLACK);
	bar(block_left,block_top,block_right,block_bottom);
	
	delimage(picture21);
}

void showa()
{
	setcaption("ballwar ! ! !") ;
	PIMAGE picture22= newimage();
	getimage(picture22, "1.png");		
	putimage(0, 0, picture22);
	
	setcolor(YELLOW);
	setfillcolor(GREEN);
	//黄线、绿色填充的圆 
	fillellipse(ball_x,ball_y,radius,radius);
	//黄线、绿色填充的挡板 
	bar(bar_left,bar_top,bar_right,bar_bottom);
		
	int block_left=block_x-32;
	int block_right=block_x+32;
	int block_top=block_y-24;
	int block_bottom=block_y+24;
	setcolor(YELLOW); 
	setfillcolor(BLUE);
	//绘制黄线蓝色填充的砖块 
	bar(block_left,block_top,block_right,block_bottom);
	
	delimage(picture22);
	Sleep(3); 
}

void updateWithoutInputa(int *p)
{
	//挡板和小球碰撞，小球反弹 
	if((ball_y+radius>=bar_top)&&(ball_y+radius<bar_bottom-(8*bar_high)/9))
	{
		if((ball_x+radius>=bar_left)&&(ball_x-radius<=bar_right))
		{ 
			ball_vy=-ball_vy;
		} 
	}else if((ball_y-radius<=bar_bottom)&&(ball_y-radius>bar_top+(8*bar_high)/9))
	{
		if((ball_x+radius>=bar_left)&&(ball_x-radius<=bar_right))
		{ 
			ball_vy=-ball_vy;
		} 
	}
	//速度设定 
	if(ball_vy<=1)
		ball_vy=2;  
	if(ball_vx<=1)
	 	ball_vx=2;
	//更新小球的坐标 
	ball_x=ball_x+ball_vx;
	ball_y=ball_y+ball_vy;
	
	//小球和边界碰撞改变方向 
	if((ball_x<=radius)||(ball_x>=Width-radius))
	{ 
		ball_vx=-ball_vx;
	} 
	if((ball_y<=radius))
	{ 
		ball_vy=-ball_vy;
	} 
	if(ball_y>=High-radius)
	{
		terminator2=0; 
		*p=0;
	}
	
	int block_left=block_x-32;
	int block_right=block_x+32;
	int block_top=block_y-24;
	int block_bottom=block_y+24;	
	if((ball_x>=block_left-radius)&&(ball_x<=block_right+radius))
	{
		if((ball_y<=block_bottom+radius)&&(ball_y>block_top+38)) 
		{
			//产生新的砖块
			block_x=random(128000)%Width; 
			block_y=random(72000)%High;
			ball_vy=-ball_vy;
			ball_vy++;
			ball_vx++; 
			//响铃
			printf("\a");
			//得分加1 
			score++;	
		}else if((ball_y>=block_top-radius)&&(ball_y<block_bottom-38))
		{
			//产生新的砖块
			block_x=random(128000)%Width; 
			block_y=random(72000)%High;
			ball_vy=-ball_vy;
			ball_vy++;
			ball_vx++; 
			//响铃
			printf("\a");
			//得分加1 
			score++;	
		}
	}
	if((ball_y<=block_bottom+radius)&&(ball_y>=block_top-radius))
	{
		if((ball_x<=block_right+radius)&&(ball_x>block_left+60))
		{
			block_x=random(128000)%Width; 
			block_y=random(72000)%High;
			ball_vx=-ball_vx;
			ball_vx++;
			ball_vy++;
			printf("\a");
			score++;
		 } else if((ball_x>=block_left-radius)&&(ball_x<block_right-60))
		 {
		 	block_x=random(128000)%Width; 
			block_y=random(72000)%High;
			ball_vx=-ball_vx;
			ball_vx++;
			ball_vy++;
			printf("\a");
			score++;
		 }
	}
	Sleep(3);
	
}

//与用户输入有关的更新 
void updateWithInput()
{
	char input;
	if(kbhit())//判断是否有输入 
	{	
		input=getch();//根据用户的不同输入来移动 ，不必输入回车 
		if(input=='a'&&bar_left>0)
		{
			bar_x=bar_x-15;//位置左移 
			bar_left=bar_x-bar_width/2;
			bar_right=bar_x+bar_width/2;
		}
		if(input=='d'&&bar_right<Width)
		{
			bar_x=bar_x+15;//位置右移 
			bar_left=bar_x-bar_width/2;
			bar_right=bar_x+bar_width/2;
		}
		if(input=='w'&&bar_top>0)
		{
			bar_y=bar_y-15;//位置上移 
			bar_top=bar_y-bar_high/2;
			bar_bottom=bar_y+bar_high/2; 
		}
		if(input=='s'&&bar_bottom<High)
		{
			bar_y=bar_y+15;//位置下移 
			bar_top=bar_y-bar_high/2;
			bar_bottom=bar_y+bar_high/2; 
		}
	}
}

//游戏结束 
void gameover()
{
	setcaption("Happy New Year ! ! !") ;
	
	PIMAGE picture3= newimage();
	//从文件中获取图像,结束背景图片 
	getimage(picture3,"aaa（1）.jpg" );
	//绘制图像，(0, 0)是图片绘制区域的左上角		
	putimage(0, 0, picture3);
	//绘制文字输出框 
	setfillcolor(EGERGB(0x0, 0x80, 0x80));
	bar(50,200, 580,360);
	//绿色字体 
	setcolor(EGERGB(0x0, 0xFF, 0x0));
	setfontbkcolor(EGERGB(0x80, 0x00, 0x80));
	setfont(80, 0, "楷体");
	//文字背景透明 
	setbkmode(TRANSPARENT);
	if(tdyam==1)
	{
		outtextxy(80, 240, "游戏结束！");
	}
	//if(tdyam==2)
	//outtextxy(80,240,"")
	delimage(picture3);
	getch();
	cleardevice();
	
	PIMAGE picture31= newimage();
	getimage(picture31, "aaa（1）.jpg");		
	putimage(0, 0, picture31);
	
	//文字背景不透明 
	setbkmode(OPAQUE); 
	setcolor(BROWN);
	setfontbkcolor(EGERGB(0x80, 0x00, 0x80));
	setfont(80, 0, "楷体");
	//输出玩家得分
	xyprintf(80,240,"游戏得分:%d",score);
	delimage(picture31);
	getch();
	cleardevice();
	
	Sleep(10);
}

//选择界面显示 
void again()
{	 
	PIMAGE picture4= newimage();
	getimage(picture4,"2.png" );		
	putimage(0, 0, picture4);
	setfillcolor(EGERGB(0x0, 0x80, 0x80));
	bar(50,200,600,360);
	setbkmode(TRANSPARENT);
	setcolor(BROWN);
	setfontbkcolor(EGERGB(0x80, 0x00, 0x80));
	setfont(40, 0, "楷体");
	outtextxy(60,220,"请选择："); 
	xyprintf(60,260,"再来一局(输入数字2~4)"); 
	xyprintf(60,300,"退出游戏(任意输入数字7~9)"); 
	
	getch();	
	delimage(picture4);
	Sleep(10);
	
 } 

//玩家按键选择游戏继续/游戏结束 
void circulation(int *p)
{
	setcaption("Happy New Years!!!") ;
	PIMAGE picture5= newimage();
	getimage(picture5,"2.png" );		
	putimage(0, 0, picture5);
	setfillcolor(EGERGB(0x0, 0x80, 0x80));
	bar(50,200,600,360);
	
	int count=0;
	key_msg keyMsg = {0};
	for (;count<1; delay_fps(10)) 
	{
		//按键消息处理
		while (kbmsg()) 
		{
			keyMsg = getkey();
			if ((keyMsg.key >= '2' )&&(keyMsg.key<='4'))
			{
				*p=1;
				count++;
				setbkmode(TRANSPARENT);
				setcaption("游戏继续");
				setfontbkcolor(EGERGB(0x80, 0x00, 0x80));
				setcolor(RED);
				setfont(60, 0, "楷体");
				outtextxy(80,220,"Pretty good!>_<");
			}
		    else if((keyMsg.key>='7')&&(keyMsg.key<='9'))
			{
				*p=0;
				count++;
				setbkmode(TRANSPARENT);
				setcolor(BROWN);
				setfontbkcolor(EGERGB(0x80, 0x00, 0x80));
				setfont(60, 0, "楷体");
			    outtextxy(80,220,"Frustrated<_>");
			}
		}
	}
	delimage(picture5);
	Sleep(10);
	
}
