#include <stdio.h>
#include <stdlib.h>
#include <string.h>



struct jucator
{
    char *nume ;
    char *prenume;
    int xp;

};
typedef struct jucator JUC;
struct echipa
{
    float scor;
    int nr_membrii;
    char *nume_echipa;
    JUC *jucator;
    struct echipa *urm;
};
typedef struct echipa ECH ;

//TEST FISIER / LISTA ---------------------------------------

void test_fisier(FILE *fp)
{
    if(fp == NULL)
    {
        printf("FISIERUL NU POATE FI DESCHIS: \n");
        exit(1);

    }
}
void test_lista(ECH *p)
{
    if(p == NULL)
    {
        printf("LISTA E GOALA");
        exit(1);
    }
}

//------------------------------------------------------------
//CALCUL PUNCTAJ ------------------------------------------
float calcul_punctaj(ECH *p)
{
    int i ;
    float suma=0,scor ;

    for(i=0 ; i < p->nr_membrii ; i++)
    {
        suma= (float) (suma+ p->jucator[i].xp) ;
    }
    scor = (float)(suma / p->nr_membrii );
    return scor;
}
//------------------------------------------------------------
//TASK 1     TASK 1     TASK  1    TASK  1     TASK   1
void cerinte(FILE *fp, int *v)
{
    fscanf(fp, "%d %d %d %d %d", &v[0], &v[1], &v[2], &v[3], &v[4]);
}
ECH *creaza_lista(FILE *fp, int n )
{
    ECH *cap = NULL  ;
    ECH *t = NULL, *p ;





    int i, j;
    for( i=0 ; i < n ; i++)
    {
        t=(ECH*)malloc(sizeof(ECH));
        fscanf(fp, "%d", &(t->nr_membrii) );
        fgetc(fp);
        t->nume_echipa = malloc(25*sizeof(char));
        fscanf(fp, "%[^\n]s", t->nume_echipa);                                            

        t->jucator =(JUC*)malloc(t->nr_membrii*sizeof(JUC));                             

        for(j=0 ; j < t->nr_membrii ; j++)
        {

            t->jucator[j].nume = malloc(25*sizeof(char));
            t->jucator[j].prenume = malloc(25*sizeof(char));
            fscanf(fp, "%s %s %d", t->jucator[j].nume, t->jucator[j].prenume, &(t->jucator[j].xp));
            //  fscanf(fp, "%s", t->jucator[j].prenume );
            // fscanf(fp, "%d", &(t->jucator[j].xp) );

        }


        t->scor = calcul_punctaj(t);
        t->urm = NULL ;

        if(cap == NULL)
        {
            cap = t ;
        }

        else
        {
            p = cap ;
            while(p->urm != NULL )
            {
                p = p->urm ;
            }
            p->urm = t ;
        }
    }

    return cap ;
}
void afiseaza(ECH *cap, FILE *fp)
{
    ECH *p ;

    for( p=cap ; p!=NULL   ; p=p->urm)
    {
        fprintf(fp, "%s\n", p->nume_echipa );
    }
}
ECH *creeaza_invers(ECH *cap)
{
    ECH *p,*r, *q ;
    p=NULL;
    q=cap;
    r=q->urm;

    while(q!=NULL)
    {
        q->urm = p ;
        p=q;
        q = r ;
        if(r!=NULL)
            r=r->urm;
    }
    return  p ;


}
//------------------------------------------------------------
//DOI LA N       SI MINIM
int doi_la_n(int nr)
{
    if(nr == 0 )
        return 0 ;
    while(nr != 1)              
    {
        if( (nr%2) != 0 )         
            return 0;
        nr = nr / 2 ;
    }
    return 1;
}
float cautare_minim(ECH *cap  )
{
    ECH *p;
    float minim=100 ;



    for(p=cap ; p !=NULL  ; p=p->urm  )
        if(minim  >  p->scor )
            minim = p->scor ;


    free(p);
    return minim;

}

//------------------------------------------------------------
//TASK 2    TASK 2    TASK  2     TASK  2
void sterge_lista(ECH **cap)
{
    ECH *p, *q ;
    int k ;
    for(q= *cap, k = 0 ; q!=NULL ; q=q->urm, k++)
        ;

    p=*cap ;
    while(doi_la_n(k)!= 1)
    {
        if((*cap)->scor == cautare_minim(*cap))
        {
            p=*cap;
            *cap=(*cap)->urm;
            free(p);
            p=*cap;
            k-- ;
        }
        else if(p->urm!=NULL && p->urm->scor == cautare_minim(*cap))
        {
            q=p->urm;
            p->urm=q->urm;
            k--;
            free(q);

        }
        else
        {
            if(p->urm==NULL)
                p=*cap;
            else
                p=p->urm;
        }
    }



}

//---------------------------------------------
//TASK  3       TASK 3      TASK  3        TASK 3      TASK 3      TASK 3
//CREEAZA COADA

struct echipaCoada
{
    ECH *echipa1 ;
    ECH *echipa2 ;
    struct echipaCoada *next ;
};
typedef struct echipaCoada ECH_C;
struct Q
{
    ECH_C *front , *rear ;
};
typedef struct Q coada ;

coada  *creeaza()
{
    coada *q;
    q=(coada*)malloc(sizeof(coada));
    if(q==NULL) return NULL ;

    q->front = q->rear = NULL ;
    return q ;
}

void enQueue(coada *q, ECH *v1 , ECH *v2)
{
ECH_C * newNode=(ECH_C *)malloc(sizeof(ECH_C ));
newNode->echipa1 = v1;
newNode->echipa2 = v2 ;
newNode->next=NULL;

if (q->rear==NULL) q->rear=newNode;
else{
(q->rear)->next=newNode;
(q->rear)=newNode;
}
if (q->front==NULL) q->front=q->rear;
}

int adauga_element_coada(coada *q, ECH *cap_lista2 )
{   int k = 0 ;
    ECH *p,*new_node=NULL  , *new_node1=NULL ;
    p=cap_lista2 ;
    while(p!=NULL)
    {
        new_node = p ;
        new_node1=p->urm ;
        enQueue(q , new_node , new_node1);
        k++ ;
        p = p->urm->urm ;
    }
    return k ;
}
int isEmpty(coada *q)
{
    return (q->front==NULL);
}

void goleste_coada(coada *q)
{
    ECH_C* temp;
    while (!isEmpty(q))
    {
        temp=q->front;
        q->front=q->front->next;

        free(temp);
    }
    //free(q);
}
//STIVE STIVE STIVE STIVE ADV
struct stiva
{
    ECH *echipa;
    struct stiva *urm ;
};

typedef struct stiva ECH_S;
void push(ECH_S **top, ECH *p )
{
    ECH_S* new_node ;
    new_node=(ECH_S*)malloc(sizeof(ECH_S));

    new_node->echipa = p ;
    new_node->urm = (*top) ;
    *top=new_node;
}

int isEmptyS(ECH_S *stiva1)
{
    return (stiva1 == NULL);
}

void goleste_stive(ECH_S **stiva1)
{
    ECH_S *temp;
    while(!isEmptyS(*stiva1))
    {
        temp = *stiva1 ;
        (*stiva1) = (*stiva1)->urm ;
        free(temp);
    }
}
void adauga1(ECH *temp)
{
    int j ;
    for(j=0 ; j < temp->nr_membrii ; j++)
        (temp->jucator[j].xp)++ ;

    temp->scor = calcul_punctaj(temp);

}
void pnm(coada *q, ECH_S **stiva1, ECH_S **stiva2, FILE *fp)
{

    ECH *temp1 = NULL , *temp = NULL;

    while(!isEmpty(q))
    {
        temp = q->front->echipa1  ;
        temp1 = q->front->echipa2 ;

        if(temp->scor >= temp1->scor)
        {
            adauga1(temp);
            push(stiva1, temp) ;
            push(stiva2, temp1) ;
            fprintf(fp, " %-25s--%25s\n", temp->nume_echipa, temp1->nume_echipa);

        }
        else if(temp->scor < temp1->scor)
        {
            adauga1(temp1);
            push(stiva1, temp1) ;
            push(stiva2, temp ) ;
            fprintf(fp, " %-25s--%25s\n", temp->nume_echipa, temp1->nume_echipa);

        }

        q->front = q->front->next;
        //free(temp);

    }




}

void creeaza_coada_finala(coada *q, ECH_S **stiva1_cap , FILE *fp )
{
    ECH_C *new_node =  NULL  ;



    while( !isEmptyS(*stiva1_cap) )
    {
        new_node = (ECH_C*)malloc(sizeof(ECH_C));
        new_node->echipa1 = (*stiva1_cap)->echipa   ;
        new_node->echipa2 = (*stiva1_cap)->urm->echipa  ;
        fprintf(fp ," %-25s---%.2f\n" , new_node->echipa1->nume_echipa , new_node->echipa1->scor);
        fprintf(fp ," %-25s---%.2f\n" , new_node->echipa2->nume_echipa , new_node->echipa2->scor);
        enQueue(q , new_node->echipa1 , new_node->echipa2) ;
  (*stiva1_cap) = (*stiva1_cap)->urm->urm ;

    }
}
void creeaza_castigator(coada *q, ECH_S **stiva1_cap , FILE *fp )
{
        ECH_C *new_node =  NULL  ;




        new_node = (ECH_C*)malloc(sizeof(ECH_C));
        new_node->echipa1 = (*stiva1_cap)->echipa   ;
        new_node->echipa2 = (*stiva1_cap)->urm->echipa  ;
        fprintf(fp ," %-25s---%.2f\n" , new_node->echipa1->nume_echipa , new_node->echipa1->scor);
        fprintf(fp ," %-25s---%.2f\n" , new_node->echipa2->nume_echipa , new_node->echipa2->scor);
        enQueue(q , new_node->echipa1 , new_node->echipa2) ;

}
//TASK4     TASK 4      TASK 4       TASK 4        TASK 4
struct arbore
{
    ECH *echipa1 ;
    struct arbore *left , *right ;
};
typedef struct arbore arbore;

arbore* newNode(ECH *ech)
{ arbore* node = (arbore*)malloc(sizeof(arbore));
node->echipa1 = ech;
node->left = NULL;
node->right = NULL;
return(node);
}

arbore* insert(arbore* node, ECH *key)
{
if (node == NULL) return newNode(key);

if (key->scor <= node->echipa1->scor) node->left = insert(node->left, key);
else if (key->scor > node->echipa1->scor)
node->right = insert(node->right, key);

return node;
}

void inorder(arbore *root , FILE *fp)
{
if (root)
{
inorder(root->right , fp);
	if(strlen(root->echipa1->nume_echipa) > 25 )
        fprintf(fp , " %-25s --- %.2f\n" , root->echipa1->nume_echipa , root->echipa1->scor ) ;
	else
	fprintf(fp , " %-25s--- %.2f\n" , root->echipa1->nume_echipa , root->echipa1->scor) ;

inorder(root->left , fp);
}
}

arbore *creeaza_coada_finala_arbore(coada *q, ECH_S **stiva1_cap , FILE *fp , arbore *root)
{
    ECH_C *new_node =  NULL  ;



    while( !isEmptyS(*stiva1_cap) )
    {
        new_node = (ECH_C*)malloc(sizeof(ECH_C));
        new_node->echipa1 = (*stiva1_cap)->echipa   ;
        new_node->echipa2 = (*stiva1_cap)->urm->echipa  ;
        fprintf(fp ," %-25s---%.2f\n" , new_node->echipa1->nume_echipa , new_node->echipa1->scor);
        fprintf(fp ," %-25s---%.2f\n" , new_node->echipa2->nume_echipa , new_node->echipa2->scor);
        enQueue(q , new_node->echipa1 , new_node->echipa2) ;
        root = insert(root , (*stiva1_cap)->echipa  ) ;
        root = insert(root , (*stiva1_cap)->urm->echipa) ;
  (*stiva1_cap) = (*stiva1_cap)->urm->urm ;

    }
    return root ;
}

int main()
{

    FILE *fp;
    ECH *cap_lista,*cap_lista2  ;
    int n, *v ;

    v=malloc(5*sizeof(int));

    fp=fopen("c.in", "rt");

    cerinte(fp, v);
    fclose(fp);


//TASK 1
    if(v[0] == 1)
    {
        fp=fopen("d.in", "rt");
        test_fisier(fp);
        fscanf(fp, "%d", &n);

        cap_lista = creaza_lista(fp, n) ;

        fclose(fp);

        fp=fopen("rezultate.out", "wt");
        cap_lista2 = creeaza_invers(cap_lista);
        afiseaza(cap_lista2, fp);


        fclose(fp);
    }

//TASK 2
    if(v[1] == 1)
    {


        sterge_lista(&cap_lista2) ;



        fp=fopen("rezultate.out", "wt");
        afiseaza(cap_lista2, fp);


        fclose(fp);

    }

//=============================
//TASK 3
//  CREEAZA COADA    q=CAPUL COZII
if(v[2] == 1)
{


       //TERMINARE TASK 3
         coada *q = creeaza() ;

int k ;
        k = adauga_element_coada(q, cap_lista2);



        fp=fopen("rezultate1.out", "wt") ;

//STIVEEEE




        ECH_S *stiva1_cap= NULL ;
        ECH_S *stiva2_cap= NULL ;

int ok = 0 ;
arbore *root=NULL ;

while(k!= 1 )
{

        if(k!= 8)
{


        pnm(q, &stiva1_cap, &stiva2_cap, fp);
        goleste_stive(&stiva2_cap);

        goleste_coada(q);
        creeaza(q) ;

        creeaza_coada_finala(q , &stiva1_cap , fp );
}
if(k==8)
{

    pnm(q, &stiva1_cap, &stiva2_cap, fp);
        goleste_stive(&stiva2_cap);

        goleste_coada(q);
        creeaza(q) ;

        root = creeaza_coada_finala_arbore(q , &stiva1_cap , fp , root );
    ok = 1 ;


}
        k=k/2 ;
}


        pnm(q, &stiva1_cap, &stiva2_cap, fp);
        fprintf(fp , " %-25s---%.2f\n" , stiva1_cap->echipa->nume_echipa , stiva1_cap->echipa->scor ) ;
       free(stiva1_cap);
       free(stiva2_cap);
        if(root == NULL) printf("sadasdsa") ;
        inorder(root, fp );

        fclose(fp);
       }






    return 0 ;

}
