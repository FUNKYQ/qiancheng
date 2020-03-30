#include<windows.h>
#include<Shlobj.h>
#include<stdio.h>
#include<math.h>
#define IDB_ONE     3301  
#define IDB_TWO     3302  
#define IDB_THREE   3303
#define IDB_FOUR    3304
#define IDB_FIVE	3305
#define IDB_SIX     3306
#define BIF_NEWDIALOGSTYLE 0x0040 

typedef struct Node
{
	int val1,val2,val3,val4;
	char val5;
	struct Node *next;
}Node;
typedef struct Node LinkList;

static LinkList *L,*head,*p = (LinkList*)malloc(sizeof(struct Node));
static int ax,ay,ex,ey,bx,by,cx,cy,tx,ty,Drawing=0;
static bool M=0,K=0;
static HDC hdc,memDC;


class MOVE
{
public:
		void GetCurClientSize(HWND hWnd, int& nWidth, int& nHeight)
		{
			RECT rc = {0};
			GetClientRect(hWnd, &rc);
			nWidth = rc.right - rc.left;
			nHeight = rc.bottom - rc.top;
		}
		int nWidth,nHeight;
		virtual void down(LinkList*,HDC,HDC)=0;
		virtual void up(LPARAM lParam)=0;
		virtual void move(HWND hwnd,LPARAM lParam)=0;
};

class RECTANGLEMOVE :public MOVE
{
public:
		void down(LinkList *p,HDC hdc,HDC memDC)
		{
			HPEN hPen1;
			hPen1 = (HPEN)GetStockObject(WHITE_PEN);
			SelectObject(memDC, hPen1);
			SelectObject(hdc, hPen1);
			Rectangle(memDC,p->val1,p->val2,p->val3,p->val4);
			HPEN hPen2 = (HPEN)GetStockObject(BLACK_PEN);
			SelectObject(memDC, hPen2);
			SelectObject(hdc, hPen2);
		}

		void up(LPARAM lParam)
		{
			ReleaseCapture();  
			SetCursor(LoadCursor(NULL,IDC_ARROW)); 
			ex = LOWORD(lParam);
			ey = HIWORD(lParam);
			Rectangle(memDC,p->val1+ex-ax,p->val2+ey-ay,p->val3+ex-ax,p->val4+ey-ay);
			p->val1=p->val1+ex-ax;
			p->val2=p->val2+ey-ay;
			p->val3=p->val3+ex-ax;
			p->val4=p->val4+ey-ay;
			p->val5='r';
		}

		void move(HWND hwnd,LPARAM lParam)
		{
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
		Rectangle(hdc,p->val1+cx-ax,p->val2+cy-ay,p->val3+cx-ax,p->val4+cy-ay);
		}

};

class CIRCLEMOVE :public MOVE
{
public:
		void down(LinkList *p,HDC hdc,HDC memDC)
		{
			HPEN hPen1;
			hPen1 = (HPEN)GetStockObject(WHITE_PEN);
			SelectObject(memDC, hPen1);
			SelectObject(hdc, hPen1);
			Ellipse(memDC,p->val1,p->val2,p->val3,p->val4);
			HPEN hPen2 = (HPEN)GetStockObject(BLACK_PEN);
			SelectObject(memDC, hPen2);
			SelectObject(hdc, hPen2);
		}

		void up(LPARAM lParam)
		{
			ReleaseCapture();  
			SetCursor(LoadCursor(NULL,IDC_ARROW)); 
			ex = LOWORD(lParam);
			ey = HIWORD(lParam);
			Ellipse(memDC,p->val1+ex-ax,p->val2+ey-ay,p->val3+ex-ax,p->val4+ey-ay);
			p->val1=p->val1+ex-ax;
			p->val2=p->val2+ey-ay;
			p->val3=p->val3+ex-ax;
			p->val4=p->val4+ey-ay;
			p->val5='c';
		}

		void move(HWND hwnd,LPARAM lParam)
		{
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
		Ellipse(hdc,p->val1+cx-ax,p->val2+cy-ay,p->val3+cx-ax,p->val4+cy-ay);
		}

};


class TRIANGLEMOVE:public MOVE
{
public:
		void down(LinkList *p,HDC hdc,HDC memDC)
		{
			HPEN hPen1;
			hPen1 = (HPEN)GetStockObject(WHITE_PEN);
			SelectObject(memDC, hPen1);
			SelectObject(hdc, hPen1);
			MoveToEx(memDC,p->val1,p->val2,NULL);
			LineTo(memDC,p->next->val1,p->next->val2);
			MoveToEx(memDC,p->next->val1,p->next->val2,NULL);
			LineTo(memDC,p->next->val3,p->next->val4);
			MoveToEx(memDC,p->val1,p->val2,NULL);
			LineTo(memDC,p->next->val3,p->next->val4);
			HPEN hPen2 = (HPEN)GetStockObject(BLACK_PEN);
			SelectObject(memDC, hPen2);
			SelectObject(hdc, hPen2);
		}

		void move(HWND hwnd,LPARAM lParam)
		{
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
			MoveToEx(hdc,p->val1+cx-ax,p->val2+cy-ay,NULL);
			LineTo(hdc,p->next->val3+cx-ax,p->next->val4+cy-ay);
			MoveToEx(hdc,p->val1+cx-ax,p->val2+cy-ay,NULL);
			LineTo(hdc,p->next->val1+cx-ax,p->next->val2+cy-ay);
			MoveToEx(hdc,p->next->val3+cx-ax,p->next->val4+cy-ay,NULL);
			LineTo(hdc,p->next->val1+cx-ax,p->next->val2+cy-ay);
		}

		void up(LPARAM lParam)
		{
				ReleaseCapture();  
				SetCursor(LoadCursor(NULL,IDC_ARROW)); 
				ex = LOWORD(lParam);
				ey = HIWORD(lParam);
				p->val1=p->val1+ex-ax;
				p->val2=p->val2+ey-ay;
				p->next->val1=p->next->val1+ex-ax;
				p->next->val2=p->next->val2+ey-ay;
				p->next->val3=p->next->val3+ex-ax;
				p->next->val4=p->next->val4+ey-ay;
				MoveToEx(memDC,p->val1,p->val2,NULL);
				LineTo(memDC,p->next->val1,p->next->val2);
				MoveToEx(memDC,p->next->val1,p->next->val2,NULL);
				LineTo(memDC,p->next->val3,p->next->val4);
				MoveToEx(memDC,p->val1,p->val2,NULL);
				LineTo(memDC,p->next->val3,p->next->val4);	
		}

};


class PRODUCT
{
public:
		int nWidth,nHeight;
		void ListInsert(LinkList *L,int val1,int val2,int val3,int val4,char val5)
		{
			LinkList *a;
			a=(LinkList *)malloc(sizeof(struct Node));
			a->val1=val1;
			a->val2=val2;
			a->val3=val3;
			a->val4=val4;
			a->val5=val5;
			a->next=L->next;
			L->next=a;
		}
		void GetCurClientSize(HWND hWnd, int& nWidth, int& nHeight)
		{
			RECT rc = {0};
			GetClientRect(hWnd, &rc);
			nWidth = rc.right - rc.left;
			nHeight = rc.bottom - rc.top;
		}
		virtual void CreateButton(HMENU)=0;
		virtual void ButtonDown(LPARAM lParam,HRGN hrgn,HWND hwnd)=0;
		virtual void MouseMove(HWND hwnd,LPARAM lParam)=0;
		virtual void ButtonUp(LPARAM lParam)=0;
};


class LINE: public PRODUCT{
public:
	void CreateButton(HMENU pop1)
	{
		AppendMenu(pop1,MF_STRING,IDB_ONE, "LINE");
	}

	void ButtonDown(LPARAM lParam,HRGN hrgn,HWND hwnd)
	{
			bx = LOWORD(lParam);
			by = HIWORD(lParam);
			hrgn=NULL;
			if(PtInRegion(hrgn,bx,by))
			{
				OutputDebugString("InRegion");				
				SetCapture(hwnd);							//设置鼠标捕获
				SetCursor(LoadCursor(NULL,IDC_SIZEALL));	//设置光标为四个方向
			}
	}

	void MouseMove(HWND hwnd,LPARAM lParam)
	{
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
		MoveToEx(hdc,bx,by,NULL);
		LineTo(hdc,cx,cy);
	}

	void ButtonUp(LPARAM lParam)
	{
		ReleaseCapture();  
		SetCursor(LoadCursor(NULL,IDC_ARROW)); 
		ex = LOWORD(lParam);
		ey = HIWORD(lParam);
		MoveToEx(memDC,bx,by,NULL);
		LineTo(memDC,ex,ey);
		ListInsert(L,bx,by,ex,ey,'l');
	}


};


class RECTANGLE: public PRODUCT{
public:
	void CreateButton(HMENU pop1)
	{
		AppendMenu(pop1,MF_STRING,IDB_TWO, "RECTANGLE");
	}

	void ButtonDown(LPARAM lParam,HRGN hrgn,HWND hwnd)
	{
			bx = LOWORD(lParam);
			by = HIWORD(lParam);
			hrgn=NULL;
			if(PtInRegion(hrgn,bx,by))
			{
				OutputDebugString("InRegion");				
				SetCapture(hwnd);							//设置鼠标捕获
				SetCursor(LoadCursor(NULL,IDC_SIZEALL));	//设置光标为四个方向
			}
	}

	void MouseMove(HWND hwnd,LPARAM lParam)
	{
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
		Rectangle(hdc,bx,by,cx,cy);
	}

	void ButtonUp(LPARAM lParam)
	{
		ReleaseCapture();  
		SetCursor(LoadCursor(NULL,IDC_ARROW)); 
		ex = LOWORD(lParam);
		ey = HIWORD(lParam);
		Rectangle(memDC,bx,by,ex,ey);
		ListInsert(L,bx,by,ex,ey,'r');
	}
	
};

class CIRCLE: public PRODUCT{
public:
	void CreateButton(HMENU pop1)
	{
		AppendMenu(pop1,MF_STRING,IDB_THREE, "CIRCLE");
	}

	void ButtonDown(LPARAM lParam,HRGN hrgn,HWND hwnd)
	{
			bx = LOWORD(lParam);
			by = HIWORD(lParam);
			hrgn=NULL;
			if(PtInRegion(hrgn,bx,by))
			{
				OutputDebugString("InRegion");				
				SetCapture(hwnd);							//设置鼠标捕获
				SetCursor(LoadCursor(NULL,IDC_SIZEALL));	//设置光标为四个方向
			}
	}

	void MouseMove(HWND hwnd,LPARAM lParam)
	{
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
		Ellipse(hdc,bx,by,cx,cy);
	}

	void ButtonUp(LPARAM lParam)
	{
		ReleaseCapture();  
		SetCursor(LoadCursor(NULL,IDC_ARROW)); 
		ex = LOWORD(lParam);
		ey = HIWORD(lParam);
		Ellipse(memDC,bx,by,ex,ey);
		ListInsert(L,bx,by,ex,ey,'c');
	}

};


class TRIANGLE: public PRODUCT{
public:
	void CreateButton(HMENU pop1)
	{
		AppendMenu(pop1, MF_STRING, IDB_SIX, "TRIANGLE");
	}

	void ButtonDown(LPARAM lParam,HRGN hrgn,HWND hwnd)
	{
			if (Drawing==1)
			{
				tx = LOWORD(lParam);
				ty = HIWORD(lParam);
				MoveToEx(memDC,bx,by,NULL);
				LineTo(memDC,tx,ty);
				MoveToEx(memDC,ex,ey,NULL);
				LineTo(memDC,tx,ty);
				ListInsert(L,bx,by,ex,ey,'0');
				ListInsert(L,tx,ty,0,0,'t');
				SendMessage(hwnd,WM_PAINT,0,0);
				return;
			}
			bx = LOWORD(lParam);
			by = HIWORD(lParam);
			hrgn=NULL;
			if(PtInRegion(hrgn,bx,by))
			{
				OutputDebugString("InRegion");				
				SetCapture(hwnd);							//设置鼠标捕获
				SetCursor(LoadCursor(NULL,IDC_SIZEALL));	//设置光标为四个方向
			}
	}

	void MouseMove(HWND hwnd,LPARAM lParam)
	{
		if (Drawing==1) return;
		SetCursor(LoadCursor(NULL,IDC_SIZEALL));
		cx=LOWORD(lParam);
		cy=HIWORD(lParam);
		nWidth = 0;
		nHeight = 0;
		GetCurClientSize(hwnd, nWidth, nHeight);
		BitBlt(
		hdc, 0, 0, nWidth, nHeight,
		memDC, 0, 0,
		SRCCOPY);
		MoveToEx(hdc,bx,by,NULL);
		LineTo(hdc,cx,cy);
	}

	void ButtonUp(LPARAM lParam)
	{
		if (Drawing==1) 
		{
			Drawing=0;
			return;
		}
		ReleaseCapture();  
		SetCursor(LoadCursor(NULL,IDC_ARROW)); 
		ex = LOWORD(lParam);
		ey = HIWORD(lParam);
		MoveToEx(memDC,bx,by,NULL);
		LineTo(memDC,ex,ey);
		Drawing=1;
	}

};


class FACTORY{
public:
	virtual PRODUCT* Product(void)=0;
	virtual MOVE* Move(void)=0;
};


class LINEFACTORY:public FACTORY{
public:
	virtual PRODUCT* Product()
	{
		return new LINE;
	}
	virtual MOVE* Move()
	{
		return 0;
	}
};


class RECTANGLEFACTORY:public FACTORY{
public:
	virtual PRODUCT* Product()
	{
		return new RECTANGLE;
	}
		virtual MOVE* Move()
	{
		return new RECTANGLEMOVE;
	}
};


class CIRCLEFACTORY:public FACTORY{
public:
	virtual PRODUCT* Product()
	{
		return new CIRCLE;
	}
		virtual MOVE* Move()
	{
		return new CIRCLEMOVE;
	}
};

class TRIANGLEFACTORY:public FACTORY{
public:
	virtual PRODUCT* Product()
	{
		return new TRIANGLE;
	}
		virtual MOVE* Move()
	{
		return new TRIANGLEMOVE;
	}
};


float Striangle(int Ax,int Ay,int Bx,int By,int Cx,int Cy)
	{
		float p=(float)(Ax * By + Bx * Cy + Cx * Ay - Ay * Bx - By * Cx - Cy * Ax);
		return (float)fabs(p/2);
	}

void GetCurClientSize(HWND hWnd, int& nWidth, int& nHeight)
{
    RECT rc = {0};
    GetClientRect(hWnd, &rc);
    nWidth = rc.right - rc.left;
    nHeight = rc.bottom - rc.top;
}


LRESULT OnPaint(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    if ( hdc != NULL && memDC != NULL )
    {
        int nWidth = 0;
        int nHeight = 0;
        GetCurClientSize(hWnd, nWidth, nHeight);
        BitBlt(
            hdc,0,0,nWidth,nHeight,
            memDC,0,0,
            SRCCOPY
        );
    }
 
    return TRUE;
}


void ListInsert(LinkList *L,int val1,int val2,int val3,int val4,char val5)
{
	LinkList *a;
	a=(LinkList *)malloc(sizeof(struct Node));
	a->val1=val1;
	a->val2=val2;
	a->val3=val3;
	a->val4=val4;
	a->val5=val5;
	a->next=L->next;
	L->next=a;
}


void OpenFile(HWND hwnd)
{
	struct Node *p1;
	p1=(struct Node *)malloc(sizeof(struct Node));
	OPENFILENAME ofn = { 0 };
	TCHAR strFilename[MAX_PATH] = { 0 };//用于接收文件名
	ofn.lStructSize = sizeof(OPENFILENAME);//结构体大小
	ofn.hwndOwner = NULL;//拥有着窗口句柄，为NULL表示对话框是非模态的，实际应用中一般都要有这个句柄
	ofn.lpstrFilter = TEXT("所有文件\0*.*\0C/C++ Flie\0*.cpp;*.c;*.h\0\0");//设置过滤
	ofn.nFilterIndex = 1;//过滤器索引
	ofn.lpstrFile = strFilename;//接收返回的文件名，注意第一个字符需要为NULL
	ofn.nMaxFile = sizeof(strFilename);//缓冲区长度
	ofn.lpstrInitialDir = NULL;//初始目录为默认
	ofn.lpstrTitle = TEXT("请选择一个文件");//使用系统默认标题留空即可
	ofn.Flags = OFN_FILEMUSTEXIST | OFN_PATHMUSTEXIST | OFN_HIDEREADONLY;//文件、目录必须存在，隐藏只读选项
	TCHAR szBuffer[MAX_PATH] = { 0 };
	BROWSEINFO bi = { 0 };
	struct _iobuf * m;
	int x3,y3;
	if (GetOpenFileName(&ofn))
	{
		MessageBox(NULL, strFilename, TEXT("选择打开的文件"), 0);
	}
	else
	{
		MessageBox(NULL, TEXT("请选择一个文件打开"), NULL, MB_ICONERROR);
	}
	m=fopen(strFilename,"rb");
	SendMessage(hwnd,WM_CREATE,0,0);
	while(!feof(m))
	{
		fread(p1,sizeof(struct Node),1, m);
		if(p1->val5=='l')
		{MoveToEx(memDC,p1->val1,p1->val2, NULL);
		LineTo(memDC,p1->val3,p1->val4);
		ListInsert(L,p1->val1,p1->val2,p1->val3,p1->val4,'l');}
		if(p1->val5=='r')
		{Rectangle(memDC, p1->val1,p1->val2,p1->val3,p1->val4);
		ListInsert(L,p1->val1,p1->val2,p1->val3,p1->val4,'r');}
		if(p1->val5=='c')
		{Ellipse(memDC, p1->val1,p1->val2,p1->val3,p->val4);
		ListInsert(L,p1->val1,p1->val2,p1->val3,p1->val4,'c');}
		if(p1->val5=='t')
		{
			x3=p1->val1;
			y3=p1->val2;
			fread(p1,sizeof(struct Node),1, m);
			MoveToEx(memDC,p1->val1,p1->val2,NULL);
			LineTo(memDC,p1->val3,p1->val4);
			MoveToEx(memDC,x3,y3,NULL);
			LineTo(memDC,p1->val3,p1->val4);
			MoveToEx(memDC,p1->val1,p1->val2,NULL);
			LineTo(memDC,x3,y3);
			ListInsert(L,p1->val1,p1->val2,p1->val3,p1->val4,'0');
			ListInsert(L,x3,y3,0,0,'t');
		}
	}
	fclose(m);
	free(p1);
	SendMessage(hwnd,WM_PAINT,0,0);
}


void SaveFile()
{
		OPENFILENAME ofn = { 0 };
		TCHAR strFilename[MAX_PATH] = { 0 };//用于接收文件名
		ofn.lStructSize = sizeof(OPENFILENAME);//结构体大小
		ofn.hwndOwner = NULL;//拥有着窗口句柄，为NULL表示对话框是非模态的，实际应用中一般都要有这个句柄
		ofn.lpstrFilter = TEXT("所有文件\0*.*\0C/C++ Flie\0*.cpp;*.c;*.h\0\0");//设置过滤
		ofn.nFilterIndex = 1;//过滤器索引
		ofn.lpstrFile = strFilename;//接收返回的文件名，注意第一个字符需要为NULL
		ofn.nMaxFile = sizeof(strFilename);//缓冲区长度
		ofn.lpstrInitialDir = NULL;//初始目录为默认
		TCHAR szBuffer[MAX_PATH] = { 0 };
		BROWSEINFO bi = { 0 };
		LPITEMIDLIST idl;
		struct _iobuf * m;
		idl = SHBrowseForFolder(&bi);
		ofn.Flags =  OFN_PATHMUSTEXIST | OFN_HIDEREADONLY | OFN_OVERWRITEPROMPT;//覆盖文件前发出警告
		ofn.lpstrTitle = TEXT("保存到");
		ofn.lpstrDefExt = TEXT("txt");//默认追加的扩展名txt
		if (GetSaveFileName(&ofn))
		{
		MessageBox(NULL, strFilename, TEXT("保存到"), 0);
		}
		else
		{
		MessageBox(NULL, TEXT("请输入一个保存的文件名"), NULL, MB_ICONERROR);
		}
		bi.hwndOwner = NULL;
		bi.pszDisplayName = szBuffer;
		bi.lpszTitle = TEXT("选择一个文件夹");//标题
		bi.ulFlags = BIF_NEWDIALOGSTYLE;
		if (SHGetPathFromIDList(idl, szBuffer))
		{
			MessageBox(NULL, szBuffer, TEXT("你选择的文件夹"), 0);
		}
		else
		{
			MessageBox(NULL, TEXT("请选择一个文件夹"), NULL, MB_ICONERROR);
		}
		m=fopen(strFilename,"wb");
		while(L)
		{
			fwrite(L,sizeof(*L),1,m);
			L = L->next;
		}
		L=head;
		fclose(m);
}


LRESULT MyInit(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
	HBITMAP memBM = NULL;
	hdc = GetDC(hWnd);
	memDC = CreateCompatibleDC(hdc);
    int nWidth = 0;
    int nHeight = 0;
    GetCurClientSize(hWnd, nWidth, nHeight);
    memBM = CreateCompatibleBitmap(hdc, nWidth, nHeight);
    HBITMAP memBMOld = (HBITMAP)SelectObject(memDC, memBM);
    DeleteObject(memBMOld);
    HPEN hPen = CreatePen(PS_SOLID, 0, RGB(0,0,0));
    HPEN hPenOld = (HPEN)SelectObject(memDC, hPen);
    HBRUSH hBrush = CreateSolidBrush(RGB(255,255,255));
    HBRUSH hBrushOld = (HBRUSH)SelectObject(memDC, hBrush);
	Rectangle(memDC, 0, 0, nWidth, nHeight);
    SelectObject(memDC, hPenOld);
    DeleteObject(hPen);
    SelectObject(memDC, hBrushOld);
    DeleteObject(hBrush);
	return 0;
}


void Message()
{
    MSG nMsg = { 0 };
    while (GetMessage(&nMsg, NULL, 0, 0))
    {
        TranslateMessage(&nMsg);
        DispatchMessage(&nMsg);
    }
}

static FACTORY *Factory=new RECTANGLEFACTORY;
LRESULT CALLBACK WndProc(HWND hwnd, UINT msg, WPARAM wParam, LPARAM lParam)
{  

	PRODUCT *shape=Factory->Product();			//还不清楚为什么不能设成全局变量
	MOVE *mov=Factory->Move();
	HMENU hRoot;
	HMENU pop1;
	CIRCLE circle;
	RECTANGLE rectangle;
	LINE line;
	TRIANGLE triangle;
	hRoot = CreateMenu();
	pop1 = CreatePopupMenu();
	HRGN hrgn=NULL;
	PAINTSTRUCT ps; 
	hdc=GetDC(hwnd);
	HBRUSH hBrush;
	hBrush = (HBRUSH)GetStockObject(NULL_BRUSH); // 获取空刷
	SelectObject(hdc, hBrush);
	SelectObject(memDC, hBrush);
	switch (msg)
	{ 
		case WM_CREATE:
			MyInit(hwnd, msg,wParam,lParam);
			AppendMenu(hRoot,MF_POPUP,(UINT_PTR)pop1, "操作");
			AppendMenu(pop1, MF_STRING, IDB_FOUR, "打开"); 
			AppendMenu(pop1, MF_STRING,  IDB_FIVE, "保存");
			line.CreateButton(pop1);
			rectangle.CreateButton(pop1);
			circle.CreateButton(pop1);
			triangle.CreateButton(pop1);
			SetMenu(hwnd,hRoot);
			break;
			
		case WM_LBUTTONDOWN:
			float S1,S2,S3,S;
			p=L->next;
			ax = LOWORD(lParam);
			ay = HIWORD(lParam);
			while(p!=NULL) 
			{
				if(((ax>p->val1&&ax<p->val3)||(ax<p->val1&&ax>p->val3))&&((ay>p->val2&&ay<p->val4)||(ay<p->val2&&ay>p->val4))&&p->val5=='r')
				{
					K=1;
					Factory=new RECTANGLEFACTORY;
					mov=Factory->Move();
					break;
				}
				else if(((ax>p->val1&&ax<p->val3)||(ax<p->val1&&ax>p->val3))&&((ay>p->val2&&ay<p->val4)||(ay<p->val2&&ay>p->val4))&&p->val5=='c')
				{
					K=1;
					Factory=new CIRCLEFACTORY;
					mov=Factory->Move();
					break;
				}
				else if(p->val5=='t'&&p->next->val5=='0')
				{
					S1=Striangle(ax,ay,p->val1,p->val2,p->next->val1,p->next->val2);
					S2=Striangle(ax,ay,p->next->val1,p->next->val2,p->next->val3,p->next->val4);
					S3=Striangle(ax,ay,p->val1,p->val2,p->next->val3,p->next->val4);
					S=Striangle(p->next->val1,p->next->val2,p->val1,p->val2,p->next->val3,p->next->val4);
					if(S==S1+S2+S3)
					{
						K=1;
						Factory=new TRIANGLEFACTORY;
						mov=Factory->Move();
						break;
					}
				}
				p=p->next;
			}
			if(K==1)
			{
				mov->down(p,hdc,memDC);
			}
			else
			{
				shape->ButtonDown(lParam,hrgn,hwnd);
			}
			M=1;
			break;
					
			case WM_MOUSEMOVE:
			if(M==1)
			{
				if(K==1)
				{
					mov->move(hwnd,lParam);
				}
				else
				{
					shape->MouseMove(hwnd,lParam);
				}
			}
			break;
		
		case WM_LBUTTONUP:
			M=0;
			if(K==1)
			{
				mov->up(lParam);
				K=0;
			}
			else
			{
				shape->ButtonUp(lParam);
			}
			break;

		case WM_COMMAND:  
				switch(LOWORD(wParam))
				{
				case IDB_ONE:			//点击LINE按钮
					Factory=new LINEFACTORY; 
					break;
                
				case IDB_TWO:			//点击RECTANGLE按钮
					Factory=new RECTANGLEFACTORY;
					break;
					
 				case IDB_THREE:			//点击CIRCLE按钮
					Factory=new CIRCLEFACTORY;
					break;
						
				case IDB_FOUR:  		//点击打开按钮
					OpenFile(hwnd);
					break;
						
				 case IDB_FIVE:			//点击保存按钮
					SaveFile();
					break;

				 case IDB_SIX:
					Factory=new TRIANGLEFACTORY;
					break;

				default:
				break;
				}
			break;

		case WM_PAINT:
			BeginPaint(hwnd, &ps);
			OnPaint(hwnd,msg,wParam,lParam);
			EndPaint(hwnd, &ps);
			break;

		case WM_DESTROY:
			PostQuitMessage(0);
			break;
 
		default:
			return DefWindowProc(hwnd,msg,wParam,lParam);
	} 
 	return 0;
}

int APIENTRY WinMain(HINSTANCE hInstance,
                     HINSTANCE hPrevInstance,
                     LPSTR     lpCmdLine,
                     int       nCmdShow)
{
    WNDCLASSEX wce = { 0 };
    wce.cbSize = sizeof(wce);
    wce.cbClsExtra = 0;
    wce.cbWndExtra = 0;
    wce.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1);
    wce.hCursor = NULL;
    wce.hIcon = NULL;
    wce.hIconSm = NULL;
    wce.hInstance = hInstance;
    wce.lpfnWndProc = WndProc;
    wce.lpszClassName = "Main";
    wce.lpszMenuName = NULL;
    wce.style = CS_HREDRAW | CS_VREDRAW;
    ATOM nAtom = RegisterClassEx(&wce);
	L=(LinkList *)malloc(sizeof(struct Node));
	head=L;
	L->next=NULL;
    if (!nAtom )
    {
        MessageBox(NULL, "注册失败", "Infor", MB_OK);
    }
	HWND hWnd = CreateWindowEx(0, "Main","画图板",
		WS_OVERLAPPEDWINDOW, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, NULL, NULL,hInstance, NULL);
	ShowWindow(hWnd, SW_SHOW);
    UpdateWindow(hWnd);
	Message();
	return 0;
}