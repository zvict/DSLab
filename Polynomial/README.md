

实验1-一元多项式 
PB17081544
张焰舒


1.	实验要求
基本功能：（要求：禁止使用STL库）
1. 输入并创建多项式（按指数升序或降序排列，系数浮点型，指数整型）
2. 输出多项式，项数+每项系数指数（按指数升序或降序排列）
3. 求和：多项式相加
4. 求差：多项式相减
5. 求值
6. 销毁
7. 清空

2.	实验内容
1、	实验流程：
仔细阅读书上有关线性链表和一元多项式加减的部分
->大致写下两一元多项式相加的过程
->实现一元多项式的相加
->以加法为基础实现相减与相乘
->综合，debug，测试
	2、实验结构
		本实验比较简单，故用单向链表组实现
		ADT polynomial{
			数据对象：D={ai|ai∈polynode,i=1,2,…,n,n≥0}
			基本操作：
				ReadPoly(&p)
				操作结果：初始化多项式。
				AddPolynomial(&p1,&p2)
				初始条件：p1,p2已存在。
				操作结果：多项式p1,p2相加，返回结果头节点。
				MinusPolynomial(&p1,&p2)
				初始条件：p1,p2已存在。
				操作结果：多项式p1,p2相减，返回结果头节点。
				MultPolynomial(&p1,&p2)
				初始条件：p1,p2已存在。
				操作结果：多项式p1,p2相乘，返回结果头节点。
		}ADT polynomial

3.	实验关键代码讲述
1、	一元多项式加法
加法是本程序的核心，乘法与减法都建立在加法基础上。基本思想是逐项比较p1和p2，对于次数的三种情况判断插入结果链表的先后顺序，以及是否要合并，当合并后系数为零还需将该项删除。
函数源码：
pPoly AddPolynomial(pPoly p1,pPoly p2)
{
    pPoly result,rear;
    result = (pPoly)malloc(sizeof(Poly));
    result->next = NULL;
    rear = result;
    int comp,sum;
 
    while (p1 && p2 ){
        comp = CompareExp(p1->exp, p2->exp);
        switch (comp){ 
//判断三种情况
            case 0:
                sum = p1->coef + p2->coef;
                if (sum)
                    AppendNode(sum,p1->exp,&rear);
							//创造新节点
                p1 = p1->next;
                p2 = p2->next;
                break;
            case 1:
                AppendNode(p1->coef,p1->exp,&rear);
                p1 = p1->next;
                break;
            case 2:
                AppendNode(p2->coef,p2->exp,&rear);
                p2 = p2->next;
                break;
        }
    }
if (p1){
//加上较长的部分
        while (p1){
            AppendNode(p1->coef,p1->exp,&rear);
            p1 = p1->next;
        }
    }
    if (p2){
        while (p2){
            AppendNode(p2->coef,p2->exp,&rear);
            p2 = p2->next;
        }
    }
 
    rear = result;
    result = result->next;
    free(rear);
return result;
//返回头节点
}
2、	一元多项式减法
减法很简单，将其中一个多项式系数求反再相加即可
函数源码：
pPoly MinusPolynomial(pPoly p1,pPoly p2)
{
	pPoly rear,result,p,pt;
	
	pt=p2;
	p = (pPoly)malloc(sizeof(Poly));
    rear = p;

    while (pt!=NULL){
        AppendNode(-pt->coef,pt->exp,&rear);
			//求系数相反数
        pt=pt->next;
    }
    rear = p;
    p = p->next;
    free(rear);
    
result=AddPolynomial(p1,p);
//相加
    return result;
}
3、	一元多项式乘法
乘法即是对于p1中的每一项与p2相乘再相加。
函数源码：
pPoly MultPolynomial(pPoly t1,pPoly t2)
{
    pPoly rear,result,temp,p1,p2;
    int coef,exp;
 
    result = (pPoly)malloc(sizeof(Poly));
    result->next = NULL;
    rear = result;
 
    if (!t1 || !t2){
        return NULL;
    }
 
    for (temp = t2; temp != NULL; temp = temp->next){
        if (t1->coef * temp->coef)
            AppendNode(t1->coef*temp->coef,t1->exp+
temp->exp,&rear);
    }
    for (p1 = t1->next; p1 != NULL; p1 = p1->next){
        for (p2 = t2; p2 != NULL; p2 = p2->next){
			//对p1中每一项与p2所有项相乘
            coef = p1->coef * p2->coef;
            exp = p1->exp + p2->exp;
 
            temp = result;
            while (temp->next->exp> exp && temp->next->next != NULL)
                temp = temp->next;

				//结果相加 
            if (temp->next->exp == exp){
                temp->next->coef += coef;
                if (temp->next->coef == 0){
                    rear = temp->next;
                    temp->next = rear->next;
                    free(rear);
                }
            }
            else if (temp->next->exp < exp){
                rear = (pPoly)malloc(sizeof(Poly));
                rear->coef = coef;
                rear->exp = exp;
                rear->next = temp->next;
                temp->next = rear;
            }
            else{
                rear = (pPoly)malloc(sizeof(Poly));
                rear->coef = coef;
                rear->exp = exp;
                rear->next = NULL;
 
                temp->next->next = rear;
            }
        }
    }
    temp = result;
    result = result->next;
    free(temp);
    return result;
}

4.	实验结果及分析
 
	要注意输入顺序，需从最高次到最低次，若要实现任意顺序输入，可以用排序算法对输入进行排序。在输出时要注意格式，特别是负数和系数为0或次数为0的情况。若要加强程序的健壮性，需要对非法输入进行判断。

5.	实验小结
本实验较简单，是线性单向链表的应用。通过本次实验，我进一步熟悉了链表的存储结构，各种算法，对于线性链表的操作也更加熟练，同时复习了指针与内存分配。需要注意的是在对链表操作时要分清对象节点，否则容易出错。


