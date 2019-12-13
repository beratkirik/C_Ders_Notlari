# İşlev Göstericileri (Funcion Pointers)
Nesnelerin nasıl adresleri varsa işlevlerin de adresleri vardır. Bir işlevin adresi, o işlevin makine kodlarının yerleştiği bellek bloğunun adresidir. `C` dilinde bir işlevin adresi, işlev göstericisi `(function pointer)` denen özel bir gösterici değişkende saklanabilir. Bir işlev adresi başka bir işleve argüman olarak gönderilebilir. Bir işlevin geri dönüş değeri bir işlev adresi olabilir. Öğeleri işlev adresleri olan diziler oluşturulabilir. İşlev adresleri `C` ve `C++` dillerinde en sık kullanılan öğeler arasındadır.

## işlev adreslerinin elde edilmesi
Bir işlevin adresi, işlev isminin adres operatörünün operandı yapılmasıyla elde edilir. Yani örneğin

```
int func(int x, int y);
```
biçiminde bildirilen `func` işlevinin adresi

```
&func
```
ifadesiyle kullanılabilir.

## işlev adreslerinin türleri
`C` dilinde her ifadenin `(expression)` bir türü vardır. Bir işlevin adresinin türü, söz konusu işlevin geri dönüş değerinin ve parametre değişkenlerinin türleriyle ilişkilendirilmiş bir adres türüdür. Öneğin yukarıda bildirilen `func` işlevinin adresi aşağıdaki türdendir:

```
int (*)(int, int)
```
Soldaki parantezin içindeki asterisk `(*)` atomu türün bir adrese ilişkin olduğunu anlatıyor. Bu parantezin soluna yazılan `int` söz konusu adrese ilişkin işlevin geri dönüş değerinin `int` türden olduğuna işaret ediyor. Sağdaki parantez içinde virgüllerle ayrılmış tür listesi ise söz konusu işlevin parametre değişkenlerinin türlerini gösteriyor.
Birkaç örnek daha verelim. Örneğin standart `strcmp `işlevinin adresi

```
int (*)(const char *, const char *)
```

türündendir. Standart `strcpy` işlevinin adresi
```
char *(*)(char *, const char *)
```
türündendir.

## işlev isimlerinin işlev adreslerine dönüştürülmesi
Bir dizi isminin bir ifade içinde kullanıldığında, derleyici tarafından dizinin ilk elemanının adresine dönüştürüldüğünü `(array to pointer conversion)` biliyorsunuz. Benzer şekilde bir işlev ismi de bir ifade içinde kullanıldığında, derleyici tarafından ilgili işlevin adresine dönüştürülür `(function to pointer conversion)`. Bir ifade içinde yer alan işlev isimlerine, yazılımsal olarak işlevlerin adresleri gözüyle bakılabilir.

```
&func
```


ile

```
func
```
eşdeğer ifadeler olarak düşünülebilir. Her iki ifade de `func` işlevinin adresi olarak kullanılabilir.

## işlev göstericisi değişkenler
Bir işlev göstericisi değişken değeri bir işlevin adresi olan değişkendir. Bu tür değişkenlerin amacı işlevlerin adreslerini tutmaktır. Yukarıda bildirilen `func` işlevinin adresini tutabilecek `fptr` isimli bir gösterici değişkeni aşağıdaki gibi tanımlayabiliriz:

```
int func(int, int);
int (*fptr)(int, int);
```
`fptr` değişkenine `func` işlevinin adresi ilk değer olarak verilebilir ya da atanabilir

```
fptr = &func;
```
işlev isimlerinin derleyici tarafından işlevlerin adreslerine dönüştürülmesinden faydalanarak bu atama deyimi aşağıdaki gibi de yazılabilirdi:

```
fptr = func;
```
Şimdi de aşağıdaki koda bakalım:

```
#include <string.h>

int main()
{
	int(*fp)(const char *, const char *) = &strcmp;
	//
	fp = &strcoll;
	//
}
```
Yukarıdaki `main` işlevinde tanımlanan `fp` isimli işlev göstericisi değişkene standart `strcmp` işlevinin adresiyle ilk değer veriliyor. Daha sonra yer alan deyimle `fp` değişkenine bu kez aynı parametrik yapıda olan standart `strcoll` işlevinin adresi atanıyor.

## işlev göstericileri ve gösterici aritmetiği
Bir işlev göstericisi gösterici aritmetiği ile kullanılamaz yani işlev adresleri diğer adresler gibi tamsayılar ile toplanamaz. İşlev göstericisi değişkenler, `++` ya da `--` operatörlerinin operandı olamazlar. Zaten gösterici aritmetiğinin işlev adresleri için bir anlamı da olamazdı. Bir işlev adresine bir tamsayı toplayıp bellekte bir sonraki işlevin adresine erişmek nasıl mümkün olurdu? İşlev adresleri içerik operatörünün (dereferencing) terimi olabilir. Böyle ifadeler ile yalnızca işlev çağrısı yapılabilir.

## işlev göstericileri ve karşılaştırma işlemleri
İşlev göstericileri karşılaştırma operatörlerinin de operandı olabilirler.  Örneğin, iki işlev göstericisinin değerlerinin aynı adres olup olmadığı yani aynı işlevi gösterip göstermedikleri  `==` ya da `!=` operatörleri ile karşılaştırılabilir:

```
#include <stdio.h>

double f1(double) { return 1.; }
double f2(double) { return 2.; }

int main()
{
	double(*fptr1)(double) = &f1;
	double(*fptr2)(double) = &f2;

	if (fptr1 == fptr2)
		printf("esit\n");
	else
		printf("esit degil\n");

	fptr1 = &f2;

	if (fptr1 == fptr2)
		printf("esit\n");
	else
		printf("esit degil\n");

	return 0;
}
```
Hiçbir işlevi göstermeyen bir işlev göstericisi değişken, değeri `NULL` gösterici adresi olan değişkendir. İşlev göstericisi değişkenler için de `NULL` gösterici semantiğinden faydalanılabilir:

```
#include <stddef.h>

int main()
{
	void(*fp)(void) = NULL;

	//
	if (fp) 
		fp();
	//
}
```
Yukarıdaki `main` işlevi içinde `fp` isimli işlev göstericisine `NULL` adresi ile ilk değer veriliyor. Daha aşağıdaki `if` deyiminde ise `fp` işlev göstericisinin değerinin NULL gösterici olup olmadığına yani fp'nin bir işlevi gösterip göstermediğine bakılıyor.

## işlev çağrı operatörü (function call operator)
İşlev çağrı operatörü tek operand alan son ek konumunda bir operatördür. Operatör öncelik tablomuzun en yüksek seviyesindedir `(primary expression)`. Bu operatörün operandı bir işlev adresidir. Operatör, programın akışını o adrese yöneltir, yani o adresteki kodun çalıştırılmasını sağlar. Örneğin:

```
func(10, 20)
```
Burada işlev çağrı operatörünün operandı func adresidir. Operatör, `func` adresinde bulunan kodu, yani `func` işlevinin kodunu çalıştırır. İşlev çağrı operatörünün ürettiği değer, çağrılan işlevin geri dönüş değeridir. Örneğin:

```
int result = strcmp(s1, s2);
```
Burada `strcmp` ifadesinin türü, geri dönüş değeri `int` parametreleri `(const char *, const char *)` olan bir işlev adresidir. Yani `strcmp` ifadesi

```
int (*)(const char *, const char *)
```
türüne dönüştürülür.

```
strcmp(s1, s2)
```
gibi bir işlev çağrısının oluşturduğu ifade ise `int` türdendir.

## işlevlerin işlev göstericileri ile çağrılması
Bir işlev göstericisinin gösterdiği işlev iki ayrı biçimde çağrılabilir. `pf` bir işlev gösterici değişkeni olmak üzere, bu değişkenin gösterdiği işlev

```
pf()
```
biçiminde, ya da

```
(*pf)()
```
biçiminde çağrılabilir. Birinci biçim daha doğal görünmekle birlikte, `pf` isminin bir gösterici değişkenin ismi mi yoksa bir işlevin ismi mi olduğu çok açık değildir. İkinci biçimde, `*pf` ifadesinin öncelik parantezi içine alınması zorunludur. Çünkü işlev çağrı operatörünün  öncelik seviyesi, içerik (dereferencing) operatörünün öncelik seviyesinden daha yüksektir. Bu çağrı biçimi kullanılan ismin bir işlev göstericisine ilişkin olduğu vurgusunu yaptığından tercih edilebilmektedir. Aşağıdaki programı derleyerek çalıştırın:

```
#include <stdio.h>

void func()
{
	printf("func()\n");
}

int main()
{
	void (*pf)(void) = func;
	
	pf();
	(*pf)();
 	
	return 0;
}
```
`main` işlevi içinde tanımlanan `pf` isimli gösterici değişkene `func` işlevinin adresi ile ilk değer veriliyor. Daha sonra `pf` işlev göstericisinin gösterdiği işlevin iki ayrı biçimde çağrıldığını görüyorsunuz.

## işlev göstericileri ve `typedef` bildirimleri
İşlev göstericilerine ilişkin kodların kolay yazılması ve okunması amacıyla çoğunlukla işlev göstericisi türlerine `typedef` bildirimleriyle eş isimler verilir. Aşağıdaki bildirime bakalım:

```
int(*func(int(*fp)(int, int)))(int, int);
```
Bu bildirimi okumak bir hayli zor değil mi? Bir de bildirimi yapana sorun:

`func`, geri dönüş değeri, geri dönüş değeri `int` türden ve iki `int` parametreli bir işlevin adresi, türünden olan ve parametresi yine geri dönüş değeri `int` türden ve iki `int` parametreli bir işlevin adresini isteyen gösterici olan bir işlev. Yani `func` işlevinin hem geri dönüş değeri hem de parametresi

```
int (*)(int, int)
```
türünden. Oysa bu türe bir `typedef` bildirimiyle eş isim verilseydi bu türe bağlı karmaşık bildirimleri okumak ya da yazmak çok daha kolay olacaktı:

```
typedef int(*Fptr)(int, int);

Fptr func(Fptr fp);

int main()
{
	Fptr fp1, fp2;
	Fptr ar_fp[10];
	Fptr *fpptr = &fp1;
	//
}
```
Yukarıdaki kodda global isim alanında, geri dönüş değeri `int` türden olan ve int türden iki parametre değişkeni olan işlevlerin adreslerinin türüne `Fptr` eş ismi veriliyor. Artık bu ismin kapsamı `(scope)` içinde bu isim bu türün karşılığı eş isim olarak kullanılabilir. Diğer bildirimlere sırasıyla bakalım:

```
Fptr func(Fptr fp);
```
Burada bildirilen `func` işlevinin hem parametre değişkeni hem de geri dönüş değeri işlev adresleri. Eğer `typedef` bildirimi olmasaydı bu bildirim

```
int(*func(int(*fp)(int, int)))(int, int);
```
biçiminde yapılacaktı.

```
Fptr fp1, fp2;
```
Burada hem `fp1` hem de `fp2` işlev göstericisi değişkenler.

```
Fptr ar_fp[10];
```
Burada `ar_fp` öğeleri `fp1`, `fp2` gibi olan, yani öğeleri işlev göstericileri olan, `10` öğeli bir dizidir.

```
Fptr *fpptr = &fp1;
```
Burada `fpptr`, `fp1` ve `fp2` gibi işlev göstericisi değişkenlerin adresini tutacak bir gösterici değişkendir `(pointer to function pointer)`.

## işlev adresi döndüren işlevler
İşlevlerin geri dönüş değerleri işlev adresleri olabilir. Bu durumda böyle bir işleve çağrı yapacak bir kod işlevden geri dönüş değeri yoluyla bir işlevin adresini alabilir:

## işlevlere işlev göndermek
Bir işlev, işinin bir kısmını gerçekleştirmesi için kendisini çağıran koddan adresini aldığı bir işlevi çağırabilir. İşlevi çağıran kod, çağırdığı işlevin işinin belirli bir kısmının kendi belirlediği gibi yapılmasını sağlayabilir.  Böylece bir işlevin davranışının bir kısmı onu çağıran kodun belirleyeceği şekilde değiştirilebilir ya da özelleştirilebilir. İşlev göstericileri ile çağrılan işlevler genelleştirilmiş işlevlerdir. Müşteri kodlar böyle işlevlere diledikleri işlevin adresini geçerek onları daha özel hale getirmiş olurlar. Bu mekanizmaya popüler olarak geri çağrı" `(call back)` denilmektedir. `C` dilinde geri çağrı mekanizması bir işlevin çağıracağı işleve bir işlevin adresini göndermesi şeklinde gerçekleşir. Bu durumda çağrılan işlevin bir parametre değişkeni bir işlev göstericisi olur. İşlev göstericilerinin en sık kullanıldığı tema budur:

```
#include <stdio.h>

void func(void(*fp)(void))
{
	printf("func cagrildi\n");
	fp();
	//
}

void f1()
{
	printf("f1 cagrildi\n");
	//
}

int main()
{
	func(f1);
}
```
Yukarıdaki kodda `func` işlevinin parametre değişkeni bir işlev göstericisi. `main` işlev içinde `func` işlevi `f1` işlevinin adresi ile çağrılıyor. `func` işlevi de adresini aldığı işlevi çağırıyor.

## türden bağımsız işlem yapan işlevler
Bazı işlevler belirli bir tür için yazılır ve dolayısıyla yalnızca belirli bir türe hizmet verirler. Örneğin öğeleri `int` türden olan bir diziyi sıralayan bir işlev şöyle bildirilebilir:

```
void sort_int_array(const int *ptr, size_t size);
```
Böyle bir işlev öğeleri double türden olan bir diziyi sıralayamaz. Bu tür durumlarda aynı kodu, farklı türlere göre yeniden yazmak gerekir. Ancak aynı işi her tür için yapabilecek, türden bağımsız olarak tek bir işlevin yazılabilmesi mümkün olabilir. Türden bağımsız işlem yapan işlevlerin gösterici parametreleri void * türünden olmalıdır. Ancak parametrelerin void * türünden olması yalnızca çağrı açısından kolaylık sağlar. İşlevi yazacak olan programcı, yine de işleve geçirilen adresin türünü saptamak zorundadır. Bunun için işleve tür bilgisine karşılık gelen bir numaralandırma değeri gönderilebilir. Örneğin herhangi bir türden diziyi sıralayacak işlevin bildirimi aşağıdaki gibi olsun:

```
void *gSort(void *parray, size_t size, int type);
```
Şimdi işlevi yazacak programcı type isimli parametre değişkenini kullanarak bir switch deyimiyle dışarıdan adresi alınan dizinin türünü saptayabilir. Ancak bu yöntem C'nin doğal türleri için çalışsa da programcı tarafından oluşturulan türlerden (user defined types) diziler için doğru çalışmaz.
Böyle genel işlevler ancak işlev göstericileri kullanılarak yazılabilir. Şimdi bir dizinin en büyük elemanın adresiyle geri dönen bir işlevi türden bağımsız olarak yazmaya çalışalım. Dizi türünden bağımsız olarak işlem yapan işlevlerin, parametre değişkenleri  tipik olarak aşağıdaki gibi olur:

```
void *g_max(const void *pa, size_t size, size_t width, int (*cmp)(const void *, const void *));
```
+ Dizinin başlangıç adresini alacak `void *` türden bir gösterici
+ Dizinin öğe sayısını alacak `size_t`` türünden bir parametre değişkeni.
+ Dizinin bir öğesinin bellekte kaç byte yer kapladığı değerini yani `sizeof` değerini alan `size_t` türünden bir parametre değişkeni.
+ Dizinin elemanlarını karşılaştırma amacıyla kullanılacak bir işlevin başlangıç adresini alan, işlev göstericisi parametre değişkeni.

Bu tür işlevlerin tasarımındaki genel yaklaşım şudur: İşlev her bir dizi öğesinin adresini gösterici aritmetiğinden faydalanarak bulabilir. Ancak dizi öğelerinin türü bilinmediğinden, işlev dizinin öğelerinin değerlerini karşılaştırma işlemini yapamaz. Bu karşılaştırmayı, işlev göstericisi kullanarak işlevi çağıran kodun gönderdiği işleve yaptırır. İşlevi çağıracak programcı, karşılaştırma işlevinin dizinin herhangi iki öğesinin adresiyle çağrılacağını göz önüne alarak karşılaştırma işlevini şöyle yazar:
Karşılaştırma işlevi, karşılaştırılacak iki nesnenin adresini alır. İşlevin birinci parametresine adresi alınan nesne, ikinci parametreye adresi alınan nesneden daha büyükse işlev pozitif herhangi bir değere, küçükse negatif herhangi bir değere, bu iki değer eşitse sıfır değerine geri döner. Bu, standart `strcmp` işlevinin sunduğu uzlaşımdır `(convention)`. Aşağıdaki programı inceleyin:

```
#include <stdio.h>
#include <string.h>

#define   MAX_NAME_LEN  20
#define   asize(a)      (sizeof(a) / sizeof(*a))

typedef struct {
	char name[MAX_NAME_LEN];
	int no;
}Person;

typedef unsigned char Byte;
typedef int(*Cmpfn)(const void *, const void *);

void *g_max(const void *pa, size_t size, size_t width, Cmpfn fp)
{
	Byte *pb = (Byte *)pa;
	void *pmax = (void *)pa;
	size_t k;

	for (k = 1; k < size; k++)
		if (fp(pb + k * width, pmax) > 0)
			pmax = pb + k * width;

	return pmax;
}

int cmp_int(const void *vp1, const void *vp2)
{
	if (*(const int *)vp1 > *(const int *)vp2)
              return 1;
        return *(const int *)vp1 < *(const int *)vp2 ? -1 : 0;

}

int cmp_person_by_name(const void *vp1, const void *vp2)
{
	return strcmp(((const Person *)vp1)->name, ((const Person *)vp2)->name);
}

int cmp_person_by_no(const void *vp1, const void *vp2)
{
	return ((const Person *)vp1)->no - ((const Person *)vp2)->no;
}

int main()
{
	int a[] = { 3, 8, 4, 7, 6, 9, 12, 1, 9, 10 };

	Person pa[] = {
		{ "Oguz Karan", 123 },{ "Kaan Aslan", 563 },{ "Ali Serce", 312 },
		{ "Necati Ergin", 197 },{ "Guray Sonmez", 297 },{ "Gurbuz Aslan", 144 } };

	Person *pper;
	int *iptr;

	iptr = (int *)g_max(a, asize(a), sizeof(int), cmp_int);
	printf("%d degerinde olan %d indisli oge max\n", *iptr, iptr - a);

	pper = (Person *)g_max(pa, asize(pa), sizeof(Person), cmp_person_by_name);
	printf("pa dizisinin en buyuk ogesi (isme gore) %s %d\n",
		pper->name, pper->no);
	pper = (Person *)g_max(pa, asize(pa), sizeof(Person), cmp_person_by_no);
	printf("per dizisinin en buyuk ogesi (numaraya gore) %s %d\n",
		pper->name, pper->no);

	return 0;
}
```
Yukarıdaki kodda,

```
typedef int(*Cmpfn)(const void *, const void *);
```
bilidirimi ile geri dönüş değeri `int` türden olan const `void *` türden iki parametresi olan işlevlerin adresi olan türe `Cmpf` eş ismi veriliyor.
Daha sonra bir dizinin en büyük öğesinin adresini döndüren türden bağımsız `g_max` isimli bir işlev tanımlanıyor. İşlevin son parametresinin bir işlev göstericisi olduğunu görüyorsunuz. `g_max` işlevi, türünü bilmediği bir dizinin başlangıç adresini, boyutunu ve öğelerinin `sizeof` değerlerini kendisini çağıran koddan alıyor. Böylece adresini aldığı dizinin türünü bilmese de bu dizinin herhangi bir öğesinin adresini gösterici aritmetiği ile hesaplayabiliyor. Dizinin iki öğesinin büyüklük karşılaştırması için son parametresine adresi geçilen işlevin çağrıldığını görüyorsunuz. İşleve çağrı yapacak tüm müşteri kodlar işlevin bu parametresine, bir karşılaştırma işlevinin adresini göndermek zorundalar. Kodda tanımlanan

```
int cmp_int(const void *, const void *);
cmp_person_by_no(const void *, const void *);
cmp_person_by_name(const void *, const void *)
```
işlevleri dizi öğelerinin karşılaştırılması amacını taşıyorlar.

Yazımızın ikinci bölümünde standart C kütüphanesinin işlev gösterici parametreli bazı işlevlerini inceleyeceğiz.
