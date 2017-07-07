# Week 16

## 關於 static 與 extern
[Stack Overflow 關於這部分的說明](https://stackoverflow.com/questions/95890/what-is-a-variables-linkage-and-storage-specifier)  

`static`
函數或是全域變數前面如果加`static`  
表示這個函數不會被 linker 看到  
因此不會和其他檔案連結  

區域變數前面如果加`static`  
表示這個變數從程式開始執行就會存在  
直到程式結束才會消失  

`extern`
只在一個原始碼檔案中定義  
```C
int x = 0;
```
其他原始碼檔案如果要使用 x，應該宣告成  
```C
extern int x;
```

`[**練習 W05_05]讓使用者輸入一個正整數，譬如 13579，利用 while 迴圈以及 % 求餘數，輸出如下的結果
The number 13579 can be written as 9 + 7*10 + 5*100 + 3*1000 + 1*10000.`

`[練習 W09_04]寫 void count_down(int n)，用 recursion 方式顯示出由 n 倒數到 0 然後再正數到 n 的數列，譬如呼叫 count_down(6) 就會顯示 6543210123456。在主程式裡測試一下作用是否正確。(注意只有顯示一個 0。)`

`[練習 WB_06]試著解釋一下，下面的程式碼的執行結果。如果把上面 f() 裡的 static 字拿掉會怎樣？ `

```C
#include <stdio.h>

int aa = 100;

int* f(void)
{
	static int aa = 3;
	
	printf("f: %d\n", aa++);
	return &aa;
}

int main(void)
{
	int *aptr;
	
	printf("main: %d\n", aa++);
	aptr = f();
	printf("main: %d\n", aa++);
	*aptr = 10;
	f(); 
	return 0 ;
}
```


## 資料處理
相關函數：
*   [qsort](http://www.gnu.org/software/libc/manual/html_node/Array-Sort-Function.html#Array-Sort-Function)
*   [fgets](http://www.cplusplus.com/reference/cstdio/fgets/)

讀取`imdb_top250.txt`檔案，裡面包含了 250 筆電影資料  
每一筆資料包含四個項目，分別是平均評分、電影名稱、上映年份、參與評分的網友數目  
每一筆資料之間用空行隔開，例如：  
```
9.2
The Shawshank Redemption
1994
885806

9.2
The Godfather
1972
641587
```

利用底下的 structure 儲存
```C
struct t_movie {
    double rating;
    char name[64];
    int year;
    int reviews;
};
typedef struct t_movie Movie;
```

產生一個 250 個元素的陣列，儲存 250 筆電影資料
```C
Movie top[250];
```

利用`qsort`將資料以底下的原則重新排列：  
1.  依照電影名稱的英文字母順序
2.  依照上映年份排序，從早期到近期，如果年份相同，則再依照電影名稱的英文字母順序排序
3.  依照平均評分的高低排序，由高到低，如果評分相同，則再依照參與評分的網友數量排序

將以上三點的的排序結果 分別輸出為`sort1.txt`、`sort2.txt`、`sort3.txt`

範例程式碼

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct t_movie {
    double rating;
    char name[64];
    int year;
    int reviews;
};
typedef struct t_movie Movie;
Movie movies[300];

int cmp1(const void *a, const void *b)
{
    Movie *s, *t;
    s = (Movie*) a;
    t = (Movie*) b;
    return strcmp(s->name, t->name);
}
int cmp2(const void *a, const void *b)
{
    Movie *s, *t;
    s = (Movie*) a;
    t = (Movie*) b;
    if (s->year > t->year) return 1;
    else if (s->year < t->year) return -1;
    else
         return strcmp(s->name, t->name);
}
int cmp3(const void *a, const void *b)
{
    Movie *s, *t;
    s = (Movie*) a;
    t = (Movie*) b;
    if (s->rating > t->rating) return -1;
    else if (s->rating < t->rating) return 1;
    else {
        if (s->reviews > t->reviews) return -1;
        else if (s->reviews < t->reviews) return 1;
        else return 0;
    }
}

void write(char * fname, Movie *mvs, int NM)
{
    int i;
    FILE *fout;
    fout = fopen(fname, "w");
    for (i=0; i<NM; i++) {
        fprintf(fout, "%3d: %f\t%s\t(%d)\t%d\n",
               i+1, mvs[i].rating, mvs[i].name, mvs[i].year, mvs[i].reviews);
    }
    fclose(fout);
}

int main(void)
{
    int NM;
    FILE *fin;

    char line[255];
    fin = fopen("imdb_top250.txt", "r");

    NM = 0;
    while (!feof(fin) ) {
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].rating = atof(line);
        if (fgets(line, 255, fin)==NULL) break;
        strcpy(movies[NM].name, line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].year = atoi(line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].reviews = atoi(line);
        NM++;
        if (fgets(line, 255, fin)==NULL) break;
    }
    fclose(fin);
/*
    for (i=0; i<NM; i++) {
        printf("%3d: %f\t%s\t(%d)\t%d\n",
               i+1, movies[i].rating, movies[i].name, movies[i].year, movies[i].reviews);
    }
*/
    qsort(movies, NM, sizeof(Movie), cmp1);
    write("sort1.txt", movies, NM);

    qsort(movies, NM, sizeof(Movie), cmp2);
    write("sort2.txt", movies, NM);

    qsort(movies, NM, sizeof(Movie), cmp3);
    write("sort3.txt", movies, NM);


    return 0;
}
```

另一種透過指標陣列的寫法

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct t_movie {
    double rating;
    char name[64];
    int year;
    int reviews;
};
typedef struct t_movie Movie;
Movie movies[300];

int cmp1(const void *a, const void *b)
{
    Movie *s, *t;
    s = * (Movie**) a;
    t = * (Movie**) b;
    return strcmp(s->name, t->name);
}
int cmp2(const void *a, const void *b)
{
    Movie *s, *t;
    s = * (Movie**) a;
    t = * (Movie**) b;
    if (s->year > t->year) return 1;
    else if (s->year < t->year) return -1;
    else
         return strcmp(s->name, t->name);
}
int cmp3(const void *a, const void *b)
{
    Movie *s, *t;
    s = * (Movie**) a;
    t = * (Movie**) b;
    if (s->rating > t->rating) return -1;
    else if (s->rating < t->rating) return 1;
    else {
        if (s->reviews > t->reviews) return -1;
        else if (s->reviews < t->reviews) return 1;
        else return 0;
    }
}

void write(char * fname, Movie * pmvs[], int NM)
{
    int i;
    FILE *fout;
    fout = fopen(fname, "w");
    for (i=0; i<NM; i++) {
        fprintf(fout, "%3d: %f\t%s\t(%d)\t%d\n",
               i+1, pmvs[i]->rating, pmvs[i]->name, pmvs[i]->year, pmvs[i]->reviews);
    }
    fclose(fout);
}

int main(void)
{
    int NM;
    FILE *fin;

    char line[255];
    fin = fopen("imdb_top250.txt", "r");

    Movie *pmovies[300];
    int i;
    for (i=0; i<300; i++) pmovies[i] = &movies[i];

    NM = 0;
    while (!feof(fin) ) {
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].rating = atof(line);
        if (fgets(line, 255, fin)==NULL) break;
        line[strlen(line)-1] = '\0';
        strcpy(movies[NM].name, line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].year = atoi(line);
        if (fgets(line, 255, fin)==NULL) break;
        movies[NM].reviews = atoi(line);
        NM++;
        if (fgets(line, 255, fin)==NULL) break;
    }
    fclose(fin);


    qsort(pmovies, NM, sizeof(Movie*), cmp1);
    write("sort1.txt", pmovies, NM);

    qsort(pmovies, NM, sizeof(Movie*), cmp2);
    write("sort2.txt", pmovies, NM);

    qsort(pmovies, NM, sizeof(Movie*), cmp3);
    write("sort3.txt", pmovies, NM);


    return 0;
}
```

## 查字典與自動完成
利用附件`words.txt`

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
enum {
    MAX_LEN = 60,
    NUM_WORDS = 120000
};

int lookup(char word[], char *dict[], int nwords);

int main(void)
{
    char **p, buf[MAX_LEN + 1];
    int i, j;
    FILE *fin;

    fin = fopen("words.txt", "r");
    p = (char **) malloc(sizeof(char *)* NUM_WORDS);
    i = 0;
    while(i< NUM_WORDS && (fgets(buf, MAX_LEN + 1, fin) != NULL)) {
        buf[strlen(buf)-1] =  '\0';
        p[i] = malloc(strlen(buf)+1);
        if (p[i] != NULL) {
            strcpy(p[i], buf);
            i++;
        }
    }
    fclose(fin);

    while(fgets(buf, MAX_LEN + 1, stdin) != NULL) {
        buf[strlen(buf)-1] = '\0';
        j = lookup(buf, p, i);
        while (j>=0) {
            if (strncmp(p[j], buf, strlen(buf))!=0) {
                j++;
                break;
            }
            j--;
        }
        if (j<0) j = 0;
        while (j<i) {
            if (strncmp(p[j], buf, strlen(buf))!=0)
                break;
            printf("%s\n", p[j]);
            j++;
        }
    }

    for (j=0; j<i; j++) {
        free(p[j]);
    }
    free(p);

    return 0;
}

int lookup(char *word, char *dict[], int nwords)
{
    int  low, high, mid, cmp;
    low = mid = 0;
    high = nwords - 1;
    while (low <= high) {
        mid = low + (high-low)/2;
        cmp = strcmp(word, dict[mid]);
        if (cmp < 0)
            high = mid - 1;
        else if (cmp > 0)
            low = mid + 1;
        else
            break;
    }
    return mid;
}
```

## 實作 Doubly Linked List
[維基百科對於 doubly linked list 的說明](http://en.wikipedia.org/wiki/Doubly_linked_list)

底下是框架，可以試著實作那些尚未被完成的功能

```C
##include <stdio.h>
#include <stdlib.h>

struct dl_node {
    int data;
    struct dl_node *prev;
    struct dl_node *next;
};
typedef struct dl_node DL_Node;

struct dl_list {
    DL_Node *firstNode;
    DL_Node *lastNode;
};
typedef struct dl_list DL_List;

void insertAfter(DL_List *list, DL_Node *node, DL_Node *newNode)
{

}

void insertBefore(DL_List *list, DL_Node *node, DL_Node *newNode)
{

}

void insertBeginning(DL_List *list, DL_Node *newNode)
{

}

void insertEnd(DL_List *list, DL_Node *newNode)
{

}

DL_Node *createNewNode(int data)
{
    DL_Node *p;
    p = (DL_Node*) malloc(sizeof(DL_Node));
    p->data = data;
    p->prev = NULL;
    p->next = NULL;
    return p;
}

void showList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        printf("%d->", p->data);
        p = p->next;
    }
    printf("NULL\n");
}
void showListReverse(DL_List *list)
{

}
void removeNode(DL_List *list, DL_Node *node)
{

}
void freeList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        p = p->next;
        if (p!=NULL)
            free(p->prev);
        else
            free(list->lastNode);
    }
    free(list);
}
int main(void)
{
    DL_List *dll = NULL;

    dll = (DL_List*) malloc(sizeof(DL_List));

    dll->firstNode = NULL;
    dll->lastNode = NULL;

    insertBeginning(dll, createNewNode(14));
    insertBeginning(dll, createNewNode(13));
    insertBeginning(dll, createNewNode(12));
    insertBeginning(dll, createNewNode(11));
    insertBeginning(dll, createNewNode(10));

    insertEnd(dll, createNewNode(20));
    insertEnd(dll, createNewNode(21));
    insertEnd(dll, createNewNode(22));
    insertEnd(dll, createNewNode(23));
    insertEnd(dll, createNewNode(24));

    showList(dll);
    showListReverse(dll);

    removeNode(dll, dll->firstNode);
    removeNode(dll, dll->lastNode);
    showList(dll);

    freeList(dll);

    return 0;
}
```

執行之後應該要得到
```
10->11->12->13->14->20->21->22->23->24->NULL
24->23->22->21->20->14->13->12->11->10->NULL
11->12->13->14->20->21->22->23->NULL
```

圖示搭配程式碼可以參考`dll.pptx`

底下是完整的程式碼

```C
#include <stdio.h>
#include <stdlib.h>

struct dl_node {
    int data;
    struct dl_node *prev;
    struct dl_node *next;
};
typedef struct dl_node DL_Node;

struct dl_list {
    DL_Node *firstNode;
    DL_Node *lastNode;
};
typedef struct dl_list DL_List;

void insertAfter(DL_List *list, DL_Node *node, DL_Node *newNode)
{
    newNode->prev = node;
    newNode->next = node->next;
    if (node->next == NULL) {
        list->lastNode = newNode;
    } else {
        node->next->prev = newNode;
    }
    node->next = newNode;
}

void insertBefore(DL_List *list, DL_Node *node, DL_Node *newNode)
{
    newNode->prev = node->prev;
    newNode->next = node;
    if (node->prev == NULL) {
        list->firstNode = newNode;
    } else {
        node->prev->next = newNode;
    }
    node->prev = newNode;
}

void insertBeginning(DL_List *list, DL_Node *newNode)
{
    if (list->firstNode == NULL) {
        list->firstNode = newNode;
        list->lastNode = newNode;
        newNode->prev = NULL;
        newNode->next = NULL;
    } else {
        insertBefore(list, list->firstNode, newNode);
    }
}

void insertEnd(DL_List *list, DL_Node *newNode)
{
    if (list->lastNode == NULL) {
        insertBeginning(list, newNode);
    } else {
        insertAfter(list, list->lastNode, newNode);
    }
}

DL_Node *createNewNode(int data)
{
    DL_Node *p;
    p = (DL_Node*) malloc(sizeof(DL_Node));
    p->data = data;
    p->prev = NULL;
    p->next = NULL;
    return p;
}

void showList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        printf("%d->", p->data);
        p = p->next;
    }
    printf("NULL\n");
}

void showListReverse(DL_List *list)
{
    DL_Node *p = list->lastNode;
    while (p != NULL) {
        printf("%d->", p->data);
        p = p->prev;
    }
    printf("NULL\n");
}

void removeNode(DL_List *list, DL_Node *node)
{
    if (node->prev == NULL) {
        list->firstNode = node->next;
    } else {
        node->prev->next = node->next;
    }
    if (node->next == NULL) {
        list->lastNode = node->prev;
    } else {
        node->next->prev = node->prev;
    }
    free(node);
}

void freeList(DL_List *list)
{
    DL_Node *p = list->firstNode;
    while (p != NULL) {
        p = p->next;
        if (p!=NULL)
            free(p->prev);
        else
            free(list->lastNode);
    }
    free(list);
}

int main(void)
{
    DL_List *dll = NULL;

    dll = (DL_List*) malloc(sizeof(DL_List));

    dll->firstNode = NULL;
    dll->lastNode = NULL;


    insertBeginning(dll, createNewNode(14));
    insertBeginning(dll, createNewNode(13));
    insertBeginning(dll, createNewNode(12));
    insertBeginning(dll, createNewNode(11));
    insertBeginning(dll, createNewNode(10));

    insertEnd(dll, createNewNode(20));
    insertEnd(dll, createNewNode(21));
    insertEnd(dll, createNewNode(22));
    insertEnd(dll, createNewNode(23));
    insertEnd(dll, createNewNode(24));

    showList(dll);
    showListReverse(dll);

    removeNode(dll, dll->firstNode);
    removeNode(dll, dll->lastNode);
    showList(dll);

    freeList(dll);

    return 0;
}

```

## Struct 練習題

 `[練習 WD_01]                                                                        
寫一個 function ptInRect() 來判斷某個點 p 是否落在某個長方形 r 裡面，是則回傳 1，否則回傳 0。  `

```C
int ptInRect(struct t_point p, struct t_rect r); 
```

`[練習 WD_02]
宣告新的型別 Rect 來代表 struct t_rect，產生指到 Rect screen 的指標，透過指標修改 screen 範圍。`


`[練習 WD_03]
寫出 function randPoint() 隨機在某個長方形 r 範圍內產生 n 個點。

```C
void randPoint(Rect r, Point p[], int n);
```


產生的點儲存在 p[] 裡面。然後再寫出 funciton meanPoint() 計算 p[] 裡頭的 n 個點的 mean。`

```C
Point meanPoint(Point p[], int n);
```

`[練習 WD_04] `

* 用圖示來解釋下面的運作原理

```C  
unsigned getBits(unsigned x, int p, int n)
{
   return ( x >> (p-n) ) & ~( ~0 << n );  /* 取出 x 的第 p 位置起 n 個 bits */
}
```

* 寫出 unsigned invert(x, p, n) 把 x 第 p 位置起 n 個 bits 由 0 變 1，1 則變為 0。
* 寫出 unsigned rightRotate(x, n) 傳回 x 向右 rotate n bits 之後的結果。


`[練習 W02_02]宣告兩個整數變數分別代表兩個正方形的邊長，計算第一個正方形和第二個正方形的面積差，然後把答案顯示在螢幕上。`

`[練習 W03_04]讓使用者輸入一星期的零用錢有多少，假設假日可以花的錢是平常的 1.5 倍，在螢幕上顯示每天可以花的數目。(譬如， Mon: 200, Tues: 200, …,Sat: 300, Sun: 300。)`

`[練習 W03_05]讓使用者輸入時速 (公里/小時)，用程式換算成 (公尺/秒) 把結果輸出到螢幕上。`

`[練習 W04_02]`

* 讓使用者輸入某個 NBA 球員的名字，接著要求輸入該球員的身高幾公分 (int)，然後用程式換算成英制單位，並在螢幕上顯示球員名字和他的身高是幾呎幾吋。(除法運算的符號是 /，譬如 x = 5 / 2;，另外如果還有些印象，整數的除法運算結果，小數點會被無條件捨去。)

* 大家目前應該和 printf() 混得蠻熟了，printf() 除了常用的 %d, %f, %s 這些格式之外，還有很多其他格式可用，詳細用法請參考課程網頁上所附的連結 The GNU C Library Reference Manual  (glibc-manual-2.2.5.pdf) 12.12 Formatted Output, p. 264。

* 雖然都沒正式提過，但是大家應該有都觀察的出來，printf() 的格式其實就是

```C
printf(control-string, item1, item2, ...);
```

* 其中 control-string 是用 "..." 指定的那一連串輸出格式設定，而 item1, item2, ... 就是你要輸出的資料。再次提醒要特別小心 control-string 裡的 %d 之類的輸出格式設定要和後面傳入的參數有一對一的對應，譬如

```C
printf(": %d ft %d in\n", "LeBron James", foot, inches);
```

* 是錯的，因為少了一個 %s，對應到 "LeBron James"。

