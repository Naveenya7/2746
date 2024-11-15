# 2746

#include <stdio.h>
#include <string.h>

struct op {
    char l;
    char r[20];
} op[10], pr[10];

int main() {
    int n, i, j, z = 0;
    char *p;
    char temp;

    printf("\nEnter the number of expressions: ");
    scanf("%d", &n);

    // Input expressions
    for (i = 0; i < n; i++) {
        printf("\nLeft: ");
        scanf(" %c", &op[i].l);
        printf("Right: ");
        scanf("%19s", op[i].r);
    }

    // Display intermediate code
    printf("\nIntermediate code\n");
    for (i = 0; i < n; i++) {
        printf("%c = %s\n", op[i].l, op[i].r);
    }

    // Dead Code Elimination
    for (i = 0; i < n; i++) {
        //temp = op[i].l;
        p = NULL;
        for (j = 0; j < n; j++) {
            p = strchr(op[j].r, op[i].l);
            if (p) {
                pr[z].l = op[i].l;
                strcpy(pr[z].r, op[i].r);
                z++;
                break;
            }
        }
    }

    // Display code after dead code elimination
    printf("\nAfter dead code elimination\n");
    for (i = 0; i < z; i++) {
        printf("%c = %s\n", pr[i].l, pr[i].r);
    }

    // Eliminate common subexpressions
    for (i = 0; i < z; i++) {
        for (j = i + 1; j < z; j++) {
            if (strcmp(pr[i].r, pr[j].r) == 0) {
                pr[j].l = pr[i].l;
            }
        }
    }

    // Display code after common subexpression elimination
    printf("\nAfter eliminating common subexpressions\n");
    for (i = 0; i < z; i++) {
        printf("%c = %s\n", pr[i].l, pr[i].r);
    }

    // Final optimization: Remove duplicate assignments
    for (i = 0; i < z; i++) {
        for (j = i + 1; j < z; j++) {
            if ((pr[i].l == pr[j].l) && (strcmp(pr[i].r, pr[j].r) == 0)) {
                pr[j].l = '\0';
                pr[j].r[0] = '\0';
            }
        }
    }

    // Display optimized code
    printf("\nOptimized code\n");
    for (i = 0; i < z; i++) {
        if (pr[i].l != '\0') {
            printf("%c = %s\n", pr[i].l, pr[i].r);
        }
    }

    return 0;
}


#include <stdio.h>
#include <ctype.h>
#include <string.h>

void keyword(char str[10]) {
    // List of keywords in C
    char keywords[7][10] = {"int", "float", "char", "while", "do", "for", "if"};
    
    // Check if str matches any of the keywords
    for (int i = 0; i < 7; i++) {
        if (strcmp(keywords[i], str) == 0) {
            printf("\n%s is a keyword", str);
            return;
        }
    }
    // If not found in keywords, it is an identifier
    printf("\n%s is an identifier", str);
}

int main() {
    FILE *f1, *f2, *f3, *f4;
    char c, str[10], st1[10];
    int num[100], tokenvalue = 0, i = 0, j = 0, k = 0;
    
    // Open file for input
    printf("\nEnter the C program (end with EOF)\n");
    f1 = fopen("input.txt", "w");
    while ((c = getchar()) != EOF) {
        putc(c, f1);
    }
    fclose(f1);

    // Open the input file for reading and other files for output
    f1 = fopen("input.txt", "r");
    f2 = fopen("identifier.txt", "w");
    f3 = fopen("specialchar.txt", "w");
    f4 = fopen("operators.txt", "w");

    // Classify each character
    while ((c = getc(f1)) != EOF) {
        // Handling constants (numbers)
        if (isdigit(c)) {
            tokenvalue = c - '0';
            c = getc(f1);
            while (isdigit(c)) {
                tokenvalue = tokenvalue * 10 + c - '0';
                c = getc(f1);
            }
            num[i++] = tokenvalue;
            ungetc(c, f1);
        }
        // Handling identifiers
        else if (isalpha(c)) {
            putc(c, f2);
            c = getc(f1);
            while (isdigit(c) || isalpha(c) || c == '_' || c == '$') {
                putc(c, f2);
                c = getc(f1);
            }
            putc(' ', f2);
            ungetc(c, f1);
        }
        // Handling operators
        else if (c == '+' || c == '-' || c == '*' || c == '<' || c == '>' || c == '/' || 
                 c == '&' || c == '%' || c == '^' || c == '=' || c == '|' || c == '!' ||
                 c == '~') {
            putc(c, f4);
        }
        // Handling special characters
        else {
            putc(c, f3);
        }
    }

    // Close all opened files
    fclose(f4);
    fclose(f2);
    fclose(f3);
    fclose(f1);

    // Display constants
    printf("\nThe constants are: ");
    for (j = 0; j < i; j++) {
        printf("%d ", num[j]);
    }
    printf("\n");

    // Process identifiers and keywords
    f2 = fopen("identifier.txt", "r");
    k = 0;
    printf("The keywords and identifiers are:\n");
    while ((c = getc(f2)) != EOF) {
        if (c != ' ') {
            str[k++] = c;
        } else {
            str[k] = '\0';
            keyword(str);
            k = 0;
        }
    }
    fclose(f2);

    // Display special characters
    f3 = fopen("specialchar.txt", "r");
    printf("\nSpecial characters are: ");
    while ((c = getc(f3)) != EOF) {
        printf("%c ", c);
    }
    fclose(f3);

    // Display operators
    f4 = fopen("operators.txt", "r");
    printf("\nOperators are: ");
    while ((c = getc(f4)) != EOF) {
        printf("%c ", c);
    }
    fclose(f4);

    printf("\n");
    return 0;
}

