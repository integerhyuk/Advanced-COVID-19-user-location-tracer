# Advanced-COVID-19-user-location-tracer
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>

#include <string.h>

#include <stdlib.h>



#define TRUE 1

#define NUM_OF_MAX 2

#define SIZE_OF_NAME 100

#define SIZE_OF_PLACE 100





struct covidtracer {

    char username[SIZE_OF_NAME];

    //Assume that users can type maximum 100 places

    int places[SIZE_OF_PLACE];

    int num_of_place;



    // for linked list

    struct covidtracer* next;



    // for tree

    struct covidtracer* r_child;

    struct covidtracer* l_child;

};



//evaluating insert function

void insert(struct covidtracer* HEAD, struct covidtracer* new) {

    struct covidtracer* cur = HEAD->next;

    struct covidtracer* prev = HEAD;



    //finding the node to insert

    while (cur != NULL) {

        //Break if the username is alphabetically greater

        if (strcmp(new->username, cur->username) < 0) {

            break;

        }



        prev = cur;

        cur = cur->next;

    }



    // Case when putting into the first node

    if (prev->next == NULL) {

        prev->next = new;

        new->next = NULL;

    }

    else {  // Case when not putting into the first node

        prev->next = new;

        new->next = cur;

    }

}



//evaluating check function (checing the overlapped venue)

void check(struct covidtracer* HEAD) {

    struct covidtracer* cur = HEAD->next;

    struct covidtracer* idx;



    //finding where to put

    while (cur != NULL) {

        idx = cur->next;

        while (idx != NULL) {

            for (int i = 0; i < cur->num_of_place; i++) {

                for (int j = 0; j < idx->num_of_place; j++) {

                    if (cur->places[i] == idx->places[j]) {

                        printf("%s and %s met in %d.\n", cur->username, idx->username, cur->places[i]);

                    }

                    else {

                        //printf("%s and %s met in %d , %d\n", cur->username, idx->username, cur->places[i], idx->places[i]);

                    }

                }

            }

            idx = idx->next;

        }

        cur = cur->next;

    }



}

void remove_node(struct covidtracer* HEAD, char* username) {

    struct covidtracer* cur = HEAD->next;

    struct covidtracer* prev = HEAD;



    //Finding the node which has to be removed

    while (cur != NULL) {

        //Break when the username is alphabetically greater

        if (strcmp(cur->username, username) == 0) {

            break;

        }



        prev = cur;

        cur = cur->next;

    }



    if (cur == NULL) {

        printf("There is no node\n");

    }

    else {  // Case when the node is being removed

        prev->next = cur->next;

        free(cur);

    }

}





void print(struct covidtracer* HEAD) {

    struct covidtracer* cur = HEAD;

    if (HEAD->next == NULL) {

        printf("Empty\n");

        return;

    }

    while (cur->next != NULL) {

        cur = cur->next;

      

        printf("%s -> ", cur->username);

    }

    printf("\n");

}



void save(struct covidtracer* HEAD, char* file_path) {

    FILE* fp = fopen(file_path, "w");

    struct covidtracer* cur = HEAD;

    if (HEAD->next == NULL) {

        fclose(fp);

        return;

    }



    while (cur->next != NULL) {

        cur = cur->next;



        fprintf(fp, "%s %d", cur->username, cur->num_of_place);

        for (int i = 0; i < cur->num_of_place; i++) {

            fprintf(fp, " %d", cur->places[i]);

        }

        fprintf(fp, "\n");

    }

    printf("\n");

    fclose(fp);

}



void load(struct covidtracer* HEAD, char* file_path) {

    FILE* fp = fopen(file_path, "r");

    char str[SIZE_OF_NAME];

    int num, place;

    struct covidtracer* new_node;

    while (1) {

        if (fscanf(fp, "%s %d", str, &num) < 2) {

            break;

        }

        new_node = malloc(sizeof(struct covidtracer));

        strcpy(new_node->username, str);

        new_node->num_of_place = num;

        for (int i = 0; i < num; i++) {

            fscanf(fp, "%d", &place);

            new_node->places[i] = place;

        }

        insert(HEAD, new_node);

    }



    fclose(fp);

}





struct covidtracer* search(struct covidtracer* HEAD, char* username) {

    struct covidtracer* cur = HEAD->next;



    while (cur != NULL) {

        if (strcmp(cur->username, username) == 0) {

            break;

        }



        cur = cur->next;

    }



    return cur;

}





int main(int argc, char* argv[]) {

    struct covidtracer HEAD; //Assume there are 10 people using this system

    char inputname[SIZE_OF_NAME];

    int menu;



    // HEAD initialize

    HEAD.next = NULL;





    while (TRUE) {

        printf("1) Introduce user\n");

        printf("2) Introduce visit to venue\n");

        printf("3) Remove user\n");

        printf("4) Check which users have been in a particular place\n");

        printf("5) Save to file\n");

        printf("6) Retrieve data from file\n");

        printf("7) Exit\n");

        scanf("%d", &menu);



        struct covidtracer* new_node;

        struct covidtracer* find_node;

        switch (menu) {

        case 1:

            new_node = malloc(sizeof(struct covidtracer));



            printf("please enter the name: ");

            scanf("%s", new_node->username);

            new_node->num_of_place = 0;



            insert(&HEAD, new_node);

            print(&HEAD);



            break;

        case 2:

            printf("Please enter your name: ");

            scanf("%s", inputname);



            

            // Have to find the specific node from the linked list

            find_node = search(&HEAD, inputname);



            // Adding the place

            if (find_node == NULL) {

                printf("This user has not been registered\n");

            }

            else {

                printf("venue : ");

                scanf("%d", &find_node->places[find_node->num_of_place++]);

            }

            break;

        case 3:

            printf("Please enter your name: ");

            scanf("%s", inputname);



            remove_node(&HEAD, inputname);

            print(&HEAD);

            break;

        case 4:

            check(&HEAD);

            break;

        case 5:

            save(&HEAD, "db.txt");

            break;

        case 6:

            load(&HEAD, "db.txt");

            break;

        case 7:

            goto finish;

            break;

        default:

            break;

        }

    }



finish:

    return 0;

}
