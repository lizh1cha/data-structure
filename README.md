#include <stdio.h>
#include <stdlib.h>

typedef char ElemType;

typedef struct ThreadNode{
	ElemType data;
	struct ThreadNode *lchild;
	struct ThreadNode *rchild;
	int ltag;
	int rtag;
}ThreadNode;

typedef ThreadNode* ThreadTree;

char str[]="ABDH##I##EJ###CF##G##";
int idx= 0; //当作下标使用 

ThreadTree prev; //创建一个前驱结点 



//创建树 
void createTree(ThreadTree *T){
	ElemType ch;
	ch=str[idx++];
	if(ch=='#'){
		*T=NULL;
	} else{
		*T=(ThreadTree)malloc(sizeof(ThreadNode));//??????*T指的是什么 
		(*T)->data=ch;
		
		createTree(&(*T)->lchild);//迭代 
		if((*T)->lchild!=NULL){
			(*T)->ltag=0;
		} 
		
		createTree(&(*T)->rchild);
		if((*T)->rchild!=NULL){
			(*T)->rtag=0;
		}
	
	}
} 


//具体线索化
void threading(ThreadTree T){
	if(T!=NULL)//T不是空树
	{
		threading(T->lchild);//迭代，左孩子的线索化 
		if(T->lchild==NULL){
			T->ltag=1;
			T->lchild=prev;
		}
		
		if(prev->rchild==NULL){
			prev->rtag=1;
			prev->rchild=T;
		}
		
		prev=T;
		threading(T->rchild);//迭代，右孩子的线索化
	} 
}
//开始线索化 
void inOrderThreading(ThreadTree *head,ThreadTree T){
	//头结点
	//头结点是独立于整个树之外的一个结点 
	*head=(ThreadTree)malloc(sizeof(ThreadNode));
	(*head)->ltag=0;
	(*head)->rtag=0;
	(*head)->rchild=(*head);//??????
	
	if(T==NULL){
		(*head)->lchild=*head;
	} else{
		(*head)->lchild=T;
		prev=(*head);
		
		threading(T);//通过迭代，遍历线索化了除最后一个以外所有的结点 
		
		//最后一个结点线索化 
		prev->rchild=*head;//????????为什么是*head 
		prev->rtag=1; 
		
		//头结点右孩子指向最后一个结点
		(*head)->rchild=prev; 
	}
} 

void inorder(ThreadTree T){
	ThreadTree curr;
	curr=T->lchild;
	
	while(curr!=T){
		while(curr->ltag==0){
			curr=curr->lchild;
		}
		
		printf("%c ",curr->data);
		
		while(curr->rtag == 1&&curr->rchild!=T){
			curr=curr->rchild;
			printf("%c ",curr->data);
		}
		curr=curr->rchild;
	}
	printf("\n");
}


int main(){
	ThreadTree head;
	ThreadTree T;
	
	//创建
	createTree(&T);
	//线索化
	inOrderThreading(&head,T);
	//基于线索遍历
	inorder(head);
	
	return 0; 
	
}
