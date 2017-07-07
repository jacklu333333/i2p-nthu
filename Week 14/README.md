# Week 14

## qsort
[C referecne 對於 qsort 的說明](http://en.cppreference.com/w/c/algorithm/qsort)

`void qsort( void *ptr, size_t count, size_t size, int (*comp)(const void *, const void *) )`
*   *ptr*  
    指向要排序的陣列的起始位置的指標
*   *count*  
    在陣列中有幾個元素要排序
*   *size*  
    在陣列中每個元素的大小，以 byte 為單位
*   *comp*  
    比較用的函數，回傳正數代表第一個引數比第二個引數小，回傳負數代表第一個引數比第二個引數大，回傳0代表兩個引數相等

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_int (const void *a, const void *b)
{
    const int *va = (const int *) a;
    const int *vb = (const int *) b;
    return *va-*vb;
}

int compare_double (const void *a, const void *b)
{
    const double *da = (const double *) a;
    const double *db = (const double *) b;
    return (*da > *db) - (*da < *db);
}

int main(void)
{
    int data1[SIZE];
    double data2[SIZE];
    int i;

    for (i=0; i<SIZE; i++) {
        data1[i] = rand()%SIZE;
        data2[i] = (double)rand()/RAND_MAX;
    }

    printf("original: ");
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    printf("  sorted: ");
    qsort(data1, SIZE, sizeof(int), compare_int);
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    printf("original: ");
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    printf("  sorted: ");
    qsort(data2, SIZE, sizeof(double), compare_double);
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    return 0;
}
```

> Note:  
> `rand()` 會回傳一個偽亂數，型別是`int`，其值介於 0 到`RAND_MAX`之間。  
> [維基百科對於偽亂數的解釋](https://zh.wikipedia.org/wiki/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%80%A7)  

如果想要觀察排序過程，可以執行下面的程式碼看看結果  

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int data1[SIZE];
double data2[SIZE];
int times;

int compare_int (const void *a, const void *b)
{
    const int *va = (const int *) a;
    const int *vb = (const int *) b;
    int i;

    times++;

    printf("exchange %2d times:",times);
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    return *va-*vb;
}

int compare_double (const void *a, const void *b)
{
    const double *da = (const double *) a;
    const double *db = (const double *) b;
    int i;

    times++;

    printf("exchange %2d times:",times);
    for (i=0; i<SIZE; i++) {
	printf("%.2f ", data2[i]);
    }
    printf("\n");

    return (*da > *db) - (*da < *db);
}

int main(void)
{
    int i;
    for (i=0; i<SIZE; i++) {
        data1[i] = rand()%SIZE;
        data2[i] = (double)rand()/RAND_MAX;
    }

    printf("  original:	");
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n\n");

    times = 0;
    qsort(data1, SIZE, sizeof(int), compare_int);

    printf("\n");
    printf("  sorted:	");
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n\n");

    printf("  original:	");
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n\n");

    times = 0;
    qsort(data2, SIZE, sizeof(double), compare_double);

    printf("\n");
    printf("  sorted:	");
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    return 0;
}
```

> Note:  
> 上例輸出僅僅只是觀察中間排序的過程，實際上`qsort`的實作並沒有被嚴格規範  
> 即使是一樣的數列，可能在不同的編譯器上會有不同的結果  



`[練習 W10_01] 用導向方式 (例如D:\COURSE\I2P\code>W10_01.exe < test.txt) 讀入一串整數，假設整數的個數不超過 100 個數，且每個整數值都介於 0 和 1,000,000 範圍之內。先把讀入的資料存入陣列中，然後一併找出其中最大值和最小值。`

* *用下列程式碼產生亂數檔"number.txt"*

```C
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int ii,max;

    FILE *random;
    random=fopen("number.txt","w");

    printf("How many number do you want?\n");
    scanf("%d",&max);
    for(ii=0;ii<max;ii++){
		fprintf(random,"%d\n",rand()%1000000);
    }

    fclose(random);
    return 0;
}
```

* *用下列程式碼找出最大最小值*
```C
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int total=0;
    int max=0, min=0;
    int number[100];

    FILE *random;

    random=fopen("number.txt","r");


    while(fscanf(random,"%d",&number[total])!=EOF)
	{
		if(max<number[total]) max=number[total];

		if(min>number[total]) min=number[total];
		total++;
	}

	printf("The max is %7d\n",number[total-1]);
	printf("The min is %7d\n",number[0]);

    return 0;
}

```


`[練習 W10_02] 和 W10_01 的輸入資料相同，把資料從小到大按照順序排好。`

* *用下列程式碼排序數列*


```C
#include <stdio.h>
#include <stdlib.h>

int compare(void *a,void *b)
{
	int *c=a;
	int *d=b;

	if(a>b) return 1;
	else	return 0;
}

int main()
{
    int total=0;
    int number[100];

    FILE *random;

    random=fopen("number.txt","r");


    while(fscanf(random,"%d",&number[total])!=EOF)
	{
		total++;
	}

	qsort(number,total,sizeof(int),compare);

	printf("The max is %7d\n",number[total-1]);
	printf("The min is %7d\n",number[0]);

    return 0;
}

```

## 對固定長度的字元陣列排序

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    int i;

    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }
    printf("\n");
    qsort(strs, SIZE, 4*sizeof(char), (int (*) (const void *, const void *))strcmp);
    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }

    return 0;
}
```

上面的方法可以正確運作的原因是字串長度固定而且連續地被放置在記憶體中  
`strs[SIZE][4]`這個二維陣列的內容如下表，`strs[0]`對應到`"aab"`，`strs[1]`對應到`"abc"`  
但是其實這樣的二維陣列，在記憶體中仍然是用一維方式循序放置  

table           |   ptr[0]  |   ptr[1]  |   ptr[2]  |   ptr[3]
----------------|-----------|-----------|-----------|-----------
ptr = strs[0]   |   `'a'`   |   `'a'`   |   `'b'`   |   `'0'`    
ptr = strs[1]   |   `'a'`   |   `'b'`   |   `'c'`   |   `'0'`   
ptr = strs[2]   |   `'a'`   |   `'a'`   |   `'a'`   |   `'0'`   
ptr = strs[3]   |   `'a'`   |   `'b'`   |   `'b'`   |   `'0'`   
......          |    ...    |    ...    |    ...    |    ...    

陣列總共有10個元素，每個陣列的元素包含 3 個英文字元外加後面跟著一個`'\0'`字元 總共 4 bytes  
因此我們可以用`qsort(strs, SIZE, 4*sizeof(char), (int (*) (const void *, const void *))strcmp);`  
讓`qsort`以陣列元素為基本單位替我們排序，也就是以 4 bytes 為單位進行個別元素的比對與搬動  
最後`strs`的內容會直接被修改，並由小排到大  

`qsort`第四個引數要強制型別轉換，讓`strcmp`符合參數型別  
原本`strcmp`的型別是`int (*) (const char*, const char*)`  
經過型別轉換之後變成`int (*) (const void*, const void*)`  

> Note:  
> 關於函數指標的型別轉換，其實上文的作法並非那麼妥當  
> 這種強制轉型其實在標準是未定義行為  
> 較為正確的作法應為再寫一個函式包住`strcmp`，並將一個指向該函式的指標作為引數傳入`qsort`  
> [Stack Overflow 關於函數指標轉型的問答](https://stackoverflow.com/questions/559581/casting-a-function-pointer-to-another-type/559671#559671)  
> [Stack Overflow 關於上文一些名詞的解釋](https://stackoverflow.com/questions/24304459/are-all-pointers-derived-from-pointers-to-structure-types-the-same)  


## 對非固定長度的字串排序

另一種方式，可以透過指標陣列，對不同長度的字串排序

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_str_ptr(const void *a, const void *b)
{
    char **pa;
    char **pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    char *ptrs[SIZE];
    int i;

    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }
    printf("\n");


    for (i=0; i<SIZE; i++) {
        ptrs[i] = strs[i];
    }
    qsort(ptrs, SIZE, sizeof(char*), compare_str_ptr);
    for (i=0; i<SIZE; i++) {
        printf("%s\n", ptrs[i]);
    }
    printf("\n");
    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }


    return 0;
}
```

`ptrs`是一個指標陣列  
因此`ptrs`的每個元素都是一個指標  都可以用來記錄某個記憶體位置  
我們先用`ptrs`的每個元素`ptrs[i]`分別記住每個字串的開始位址  
```C
for (i=0; i<SIZE; i++) {
    ptrs[i] = strs[i];
}
```

接下來對指標陣列進行排序  
依照`ptrs`的每個元素所指到的字串用`strcmp`進行比較以後，將`ptrs`的元素搬動  
所以只是調換指標的順序(也就是`ptrs`元素的順序)，實際的資料`strs`不會被更改  

請注意這時候`compare`函數的寫法
```C
int compare_str_ptr(const void *a, const void *b)
{
    char * *pa;
    char * *pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}
```

*   被搬動的東西是指標
*   用來比較則是指標所指到的字串

我們可以換個格式顯示陣列內容，執行底下附的程式碼會輸出下表的內容  
對照記憶體位址以及`strs`的內容就會看出端倪

```
strs: aab0|abc0|aaa0|abb0|acb0|aab0|abc0|aaa0|abb0|acb0|
ptrs: 0028FEE4|0028FEE8|0028FEEC|0028FEF0|0028FEF4|0028FEF8|0028FEFC|0028FF00|0028FF04|0028FF08|
after sorting
ptrs: 0028FF00|0028FEEC|0028FEE4|0028FEF8|0028FEF0|0028FF04|0028FEE8|0028FEFC|0028FF08|0028FEF4|
ptrs: aaa0|aaa0|aab0|aab0|abb0|abb0|abc0|abc0|acb0|acb0|
strs: aab0|abc0|aaa0|abb0|acb0|aab0|abc0|aaa0|abb0|acb0|
```

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_str_ptr(const void *a, const void *b)
{
    char **pa;
    char **pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    char *ptrs[SIZE];
    int i;

    printf("strs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", strs[i]);
    }
    printf("\n");

    for (i=0; i<SIZE; i++) {
        ptrs[i] = strs[i];
    }

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%p|", ptrs[i]);
    }
    printf("\nafter sorting\n");

    qsort(ptrs, SIZE, sizeof(char*), compare_str_ptr);

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%p|", ptrs[i]);
    }
    printf("\n");

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", ptrs[i]);
    }
    printf("\n");

    printf("strs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", strs[i]);
    }

    return 0;
}
```

## Pointers to Functions

[The GNU C Programming Tutorial 對於 function pointers 的解釋](http://www.crasseux.com/books/ctutorial/Function-pointers.html)  

`void qsort( void *ptr, size_t count, size_t size, int (*comp)(const void *, const void *) )`  

以`qsort`來說，當初這個函式是被設計成對不同型別的資料都能進行排序  
在這邊可以理解成，其實這個函式根本不知道它正在排序的型別是什麼 (`ptr`的型別對`qsort`內部來說是`void*`)  
它只知道一個元素多大、總共有幾個元素和呼叫者提供的函式可以比較兩個元素的先後順序  
要傳入的`comp`才是實際上知道元素的型別，並如何比較兩者大小的關鍵  

所以，如果想要設計一種函式，是希望能對應不同需求，執行使用者根據規定所設計出的函式  
這種時候就可以用函數指標，為程式帶來更大的彈性  
以`qsort`來說，它對使用者要求`comp`就是接收兩個只能讀取的`void*`指標，並回傳`int`代表比較結果的函式

```C
#include <stdio.h>
#include <stdlib.h>
int sum(int a[], int n)
{
    int i, ans = 0;
    for (i=0; i<n; i++) {
        ans += a[i];
    }
    return ans;
}
int sum_squared(int a[], int n)
{
    int i, ans = 0;
    for (i=0; i<n; i++) {
        ans += a[i]*a[i];
    }
    return ans;
}

int middle(int a[], int n)
{
    return a[n/2];
}

int run (  int  (*fp) (int *, int )  , int a[], int n)
{
    return fp(a, n);
}

int main(void)
{
    int a[] = {1, 2, 3, 4};
    printf("%d\n", run(sum, a, 4));
    printf("%d\n", run(sum_squared, a, 4));
    printf("%d\n", run(middle, a, 4));
    return 0;
}
```

從上例中，可以看出函式指標的語法：  
*   若要宣告一個指向函式的指標，可以寫`return _type (*pointer_name) (parameter_list)`  
    而那個指標的型別是`return _type(*)(parameter_list)`  
*   而一個函式可以被隱式地轉換為指向該函式的指標，也可以透過`&function_name`來取得函數位址  
    所以如下兩個都是合法的：
    *   `run(sum, a, 4));`  
    *   `run(&sum, a, 4));`
*   要使用該函式指標的話，可以有如下兩種用法：
    *   `fp(a,n);`
    *   `(*fp)(a,n);`

> Note:  
> [Stack Overflow 關於函式指標語法的有趣問答](https://stackoverflow.com/questions/6893285/why-do-function-pointer-definitions-work-with-any-number-of-ampersands-or-as)  
> [如何解讀型別](http://ieng9.ucsd.edu/~cs30x/rt_lt.rule.html)  

## 在程式執行期間取得記憶體
[C reference 對於 malloc 的說明](http://en.cppreference.com/w/c/memory/malloc)  
[C reference 對於 free 的說明](http://en.cppreference.com/w/c/memory/free)  

有的時候，我們需要在執行的時候動態分配記憶體  
此時可以使用`malloc`跟`free`進行記憶體的分配與釋放  

`void* malloc( size_t size )`  
*   *size*  
    要分配的記憶體大小  
*   *回傳值*  
    如果成功的話，回傳的指標是指向分配的的記憶體的開頭位址  
    如果失敗的話，回傳一個空指標

`void free( void* ptr )`  
*   *ptr*  
    要釋放的記憶體的指標，必須是當初分配某塊空間時拿到的那個位址；如果`ptr`為`NULL`，則此函式不會做任何事情  
    譬如呼叫`malloc(16);`以後拿到的位址是`0x8BADF00D`，之後若想要將這塊塊記憶體回收  
    在呼叫`free`時必須傳入`0x8BADF00D`，不能是`0x8BADF00F`或其他不是由相關記憶體分配函式所回傳的位址  

> Note:  
> `void*`的意思是指向不特定的型別，通常用來記錄某個實體是位在記憶體的哪裡  
> 所以針對`void*`是不能進行運算及間接引用  
> 在轉型時要特別注意，通常是建議如果將`T*`轉為`void*`，最後要間接引用時，應轉回`T*`而非其他型別  
> `int*`->`void*`->`int*`會得到與原本相同的位址，最後間接取值時會正確  
> `int*`->`void*`->`float*`不一定會得到與原本相同的位址，最後間接取值時的結果也不一定會正確  
> [Stack Overflow 關於指標轉換的問答](https://stackoverflow.com/questions/4810417/c-when-is-casting-between-pointer-types-not-undefined-behavior)  
> [Stack Overflow 關於 void* 轉換的問答](https://stackoverflow.com/questions/20469958/c-when-is-casting-void-pointer-needed)  

```C
/* E10_15.c */
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
   double *ptd;
   int array_size, i;

   printf("How many doubles do you want? ");
   scanf("%d", &array_size);
   ptd = (double *) malloc(array_size * sizeof (double));
   if (ptd == NULL) {
      printf("Memory allocation failed.\n");
      exit(EXIT_FAILURE);
   }

   for (i = 0; i < array_size; i++) {
      ptd[i] = (double) rand() / RAND_MAX;
   }
   for (i = 0; i < array_size; i++) {
      if (*(ptd+i) > 0.5)
         printf("%d: %f\n", i, ptd[i]);
   }

   free(ptd);

   return 0;
}
```

```C
/* W10_09.c */
#include <stdio.h>
int main(void)
{
   int *a, **b, i, j, rows, cols;
   
   scanf("%d %d", &rows, &cols);
   a = (int *) malloc(rows * cols * sizeof(int));
   for (i = 0; i < rows*cols; i++) a[i] = i;
   b = (int* *) malloc(rows * sizeof(int *));

   for (i = 0 ; i < rows; i++) b[i] = &a[i*cols];


   for (i = 0; i < rows; i++) {
      for (j = 0; j < cols; j++) {
         printf("%3d ", b[i][j]); 
      }
      printf("\n");
   }
   
   free(b);
   free(a);

   return 0;
}
```

關於動態分配記憶體時，可能會犯的一些錯誤：  
*   memory leak  
    ```C
    int* a = malloc(sizeof(int));
    *a = 123;
    a = malloc(sizeof(int));
    *a = 456;
    free(a);
    ```  
    以上例來說，第一次分配到的記憶體就這樣被丟失，沒有被回收，之後也沒有任何方法能夠回收到那塊記憶體  
    類似這種狀況，當有記憶體是沒辦法被回收的，就被稱為 memory leak  

*   dangling pointer  
    ```C
    int* ptr = NULL;
    {
        int a = 0;
        ptr = &a;
    }
    // ptr is a dangling pointer now !
    ```  
    以上例來說，`int a`的生命週期只在那對大括弧內，一旦過了那個大括弧的範圍，`ptr`所指向的位址就是不合法的  
    類似這種狀況，當有指標指向的記憶體是已經無效的，該指標就被稱為 dangling pointer  
    ```C
    int* a = malloc(sizeof(int));
    int* b = a;
    *a = 123;
    
    free(a);
    a = NULL;
    
    // b is a dangling pointer now !
    ```  
    > Note:  
    > 對大多數`malloc`和`free`的 implementation 來說，所謂取得和釋放其實只是改變記憶體的使用狀態  
    > `malloc`和`free`做的事情只是去記錄哪些記憶體區塊被佔用以及哪些可用  
    > 所以呼叫`free(a)`之後，乍看之下可能會覺得沒發生什麼變化，因為原先那些記憶體內的資料可能都還存在  
    > 但是無論如何程式都不該再去存取已經被釋放的記憶體內容  

## 字元陣列和字串
[C reference 對於 string literal in C 的說明](http://en.cppreference.com/w/c/language/string_literal)  
[C++ reference 對於 string literal in C++ 的說明](http://en.cppreference.com/w/cpp/language/string_literal)  
[Stack Overflow 關於 string literal 在 C 與 C++ 中的說明](https://stackoverflow.com/questions/2245664/what-is-the-type-of-string-literals-in-c-and-c)  

字元陣列其實就是一個陣列，其元素為字元  
而字串則是一個字元陣列，且其結尾必須是`'\0'`  
通常在傳遞字串時，會用指向字串的第一個字元的指標作為該字串的代表  

關於 string literal，有幾個需要注意的地方：  
1.  string literal 的型別是 `char[]`  
2.  如果是用指標去指向某個 string literal，那麼其指向的內容是不可寫的  
    `char* str = "Test"; // OK`  
    `str[0] = 't'; // 未定義行為，但可以通過編譯，可能在執行時出錯`  
3.  為了避免類似錯誤發生，通常會建議用：  
    *  `const char* str = "string"; // 如果寫出 str[0]='t'，在編譯時就會出錯`  
    *  `char str[] = "string"; // str[0]='S' 是可以的`  

對於`const char* str = "string";`：  
*   `"string"`會在程式被載入的時候，一起被載入到某塊記憶體 (常見的狀況是載入到 data segment)  
*   而`str`指向的位址就是`"string"`被儲存到的記憶體的開頭位址  
*   標準規定對於 string literal 的寫入是未定義行為，實際上也有實作是將`"string"`載入到一塊只允許讀取的記憶體  

對於`char str[] = "string";`：  
*   其實該句等同`char str[7] = "string"`，也等同於`char str[7] = {'s', 't', 'r', 'i', 'n', 'g', '\0'};`  
*   相當於把記憶體中`"string"`複製一份當作`str`這個陣列的初始值  
*   所以`str[0] = 'S';`就合法了  

```C
#include <stdio.h>

int main(void)
{

    char *str1[] = {"piece", "of", "cake"};

    char str2[][8] = {"piece", "of", "cake"};

    int i, j;

    for (i=0; i<3; i++) {

        for (j=0; j<8; j++)

            printf("%c", str1[i][j]);

        printf("\n");

    }

    for (i=0; i<3; i++) {

        for (j=0; j<8; j++)

            printf("%c", str2[i][j]);

        printf("\n");

    }

    return 0; 

}
```

## 雙重指標

相當於指標的指標  
如果想要透過一個函式更改外面的`int`變數，會有類似底下的寫法：  
```C
void swap(int* a, int* b)
{
    int temp = *a;
    *a = *b;
    *b = temp;
}

...

int x = 5, y = 3;
swap(&x, &y);
```  

那如果想要透過一個函式更改外面的`int*`變數呢？  
觀察上面的程式，會發現如果要透過函式更改外面的變數，其型別為`T`  
那麼在函式的參數，會期待收到一個`T*`的引數，藉此可以透過間接引用的方式改到外面的變數  

下例則是表達如果希望藉由函式改變外面的`ptr`，其型別為`float*`  
在這裡可以將`float*`理解成`T`，按照上面的推論，那麼必須要傳入型別為`float**`的引數，也就是`&ptr`  
才可以在函式裡面藉由`*p`更改`ptr`所指向的位址  

```C
#include <stdio.h>
#include <stdlib.h>

void malloc_float2( float * * p , unsigned int sz)
{
    *p = (float *) malloc(sz*sizeof(float));
}

int main(void)
{
    float * ptr = NULL;
    int i;
    int n = 100;

    malloc_float2(&ptr, n);

    for (i=0; i<n; i++)
        ptr[i] = (float) rand()/RAND_MAX;

    free(ptr);

    return 0;
}
```
`[練習 W10_08] 根據變數的宣告來判斷下列動作是否合法。`

```C
pt = &a[0][0];
pt = a[0];
pa = a; 
pa = b; 
p2 = &pt;
*p2 = b[0];
p2 = b;
```

* *宣告型式：*

```C
int *pt;
int (*pa)[3];
int a[2][3];
int b[3][2];
int **p2;
```

`[練習 WA_01]
請用圖示方式來表示 b 和 bp 的關聯。假如 b 所儲存的值為 3，而 b 這個變數的記憶體位址是 100000。`



`[練習 WA_02]
產生一個 float 變數 x 和一個指向 float 的指標 xp，先直接把 x 的值設為 3.1，然後再透過 xp 讓 x 的值增加 0.04。`



`[練習 WA_03]
試著用 debugger 來 trace 主程式呼叫 swap() 的過程，看看局部變數 a 和 b 什麼時候會存在。
大家應該都已經非常熟悉 swap()，知道要改成下面的寫法才對`

```C
void swap(double *a, double *b)
{
   double tmp; 
   tmp = *a;
   *a = *b;
   *b = tmp;
}
```

`[練習 WA_04]
寫一個 function 傳入整數值 n，求出 1+2+...+n 和 1*1+2*2+...+n*n 並把答案分別儲存到主程式中的兩個變數 m 和 s 中。`

`[練習 WA_05]
假如是 char *pa; 則 (pa+1) 和 pa 差多少？`
由此衍伸出來，我們得到四種存取陣列元素的寫法，

```C
int a[10], *pa;
pa = &a[0];
*pa = 1;
pa[1] = 2;
a[2] = 3;
*(a + 3) = 4;
```

* 其中第四種用法要稍微注意一下，陣列名稱 a 代表陣列開頭元素的位址，但是它並不是一個指標變數，程式compile 之後它所代表的相對位址就會確定，譬如在 1000 這個位址, 而 &a[2] 就變成 1008, 而 (a + 3) 就變成 1012。所以 a++ 這樣的動作對陣列來說是不行的；雖然陣列的名稱在大多數情況下用法和指標沒兩樣，但是它終究只是那一連串陣列元素的代稱(代表著開頭位址)，而不是一個指標變數，所以不能去改變它的值。(實際上， compiler 看到 a[2] 這樣的寫法時，會把它翻譯成 *(a+2*sizeof(int))，然後再把 a 用位址取代。)
除此此外，在 function 的參數宣告中 void f(int ary[]) 和 void f(int *ary) 陣列與指標這兩種不同寫法是完全相通的。(可能會讓人覺得比較奇怪的是對於 int ary[] 這種用法，在 f() 裡頭可以使用 ary++，因為對 function 來說，參數 ary 其實是個存放在堆疊記憶體中的變數，不過這已經有些離題了。)
同樣的，呼叫時傳遞位址也可以用兩種方式
f(a);
f(&a[0]);
甚至如果有需要，不一定要傳陣列的開頭位址，譬如
f(a + 2);
f(&a[2]); 



* 而呼叫的地方應該改成 swap(&x, &y); 因為我們必須把存放 x 和 y 兩個變數的記憶體位址 &x 和 &y 告訴 swap()，讓 swap() 到那兩個位址去找，參數傳進去後相當於做了 a = &x; 和 b = &y; 的動作。然後用 *a 和 *b 來設定那兩個記憶體位址中所儲存的值。



`[練習 WA_06] 
寫個小程式測試一下，如果 pa = &a[2]; 那麼 pa[-1] 或 *(pa-2) 這樣的用法可以用嗎？`


`[練習 WA_07]
寫一個 function f()，傳入兩個各自已經由小到大排序好的正整數陣列 a 和 b, 以及一個比 a 和 b 合起來還長的空陣列 c, (假設 a 和 b 的最後一個元素都是 -1 代表陣列結束),f() 要將 a 和 b 合併並且從大到小
排好儲存在 c 裡頭。`

`[練習 WA_08]

說明並圖示下面兩種寫法的差異：

```C
char str1[] = "piece of cake";
char *str2 = "piece of cake"; /* 附註：ANSI C 要求 compiler 可以支援到長度 509 的字串`
```

`[練習 WA_09]
假設在 main() 裡面有下列三行，請問 str1， str2， str3 的內容會是什麼？`

```C
char str1[100];
char str2[100] = {'a'}; 
char str3[100] = "";
```

`[練習 WA_10]
寫一個 function 傳入一個字串，傳回該字串的長度 (整數值)。`

`[練習 WA_11]
寫一個 function f()，傳入一個字串，判斷是否為 palindrome (像是 "level", "wasitacatisaw")。`

`[練習 WA_12]
用圖示表現出下面的字串`

```C
char *ptrary[] = {"piece", "of", "cake"};
```


`[練習 WA_13]
圖示下面兩種寫法的差別`

```C
char *str1[] = {"piece", "of", "cake"};
char str2[][8] = {"piece", "of", "cake"};
```

`[練習 WA_14]`

* 二維陣列使用起來很方便，譬如用來儲存矩陣或影像。但是在 C 程式中我們必須預先給定陣列大小，譬如
int a[10][20];。這個練習是要藉由指標陣列來模擬動態產生二維陣列的機制。
假設程式最前面已經定了兩個 global 陣列：char buf[1000000]; 是一個字元陣列。char *bptr[1000]; 是一個字元指標的陣列。
試著寫出一個 function 叫做 char ** mtx(int m, int n); 傳入的參數 m 和 n 代表我們想要產生的二維陣列的大小，回傳值則是一個指向指標的指標。 mtx() 要替我們設定 bptr 的指標內容 (指到 buf)，然後傳回一個指標，讓我們能用例如 p = mtx(5, 10); 的方式取得一個二維陣列，然後就可以在程式把 p (type 是 char **) 當作二維陣列來使用，譬如 p[2][5] = 7。在　mtx() 裡面應該會包含兩個 static 變數，用來記住已經被使用掉的 buf 和 bptr 有多少。如果　mtx() 發現沒有足夠的空間產生二維陣列，就把回傳值設為 NULL。

![Alt text](ptr/WA_14.png)
   

`[練習 W09_09] 寫一個 function 叫做 sort2()，它的作用是讓兩個變數 a 和 b 在經過呼叫 sort2() 之後，a 的值變成 a 和 b 之中較小的值，同時 b 的值被改成 a 和 b 之中較大的值。試著用指標和位址存取方式來寫。`

Bits運算：

`[練習 W07_03]令 x = 5; 而 y = 3;，判斷下列 expressions 是 true 還是 false。`


```C
x > 2 && y == 3
!(x < 2 || y == 3)
x != 0 && y
x == 0 || !y
x != y && (20/x) < y 
!(y > 5 || y < 2) && !(x%5)
!x && x

```

##Static：

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
