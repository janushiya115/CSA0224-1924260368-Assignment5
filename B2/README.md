#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define SIZE 10

/* Check valid blood groups */
int validGroup(const char *group)
{
    const char *groups[] =
    {
        "A+", "A-", "B+", "B-",
        "AB+", "AB-", "O+", "O-"
    };

    for (int i = 0; i < 8; i++)
    {
        if (strcmp(group, groups[i]) == 0)
            return 1;
    }

    return 0;
}

/* Test 1: Unsafe scanf */
void testScanf()
{
    char name[SIZE];

    printf("Enter donor name: ");

    scanf("%s", name);

    printf("Donor name: %s\n", name);
}

/* Test 2: Safe fgets */
void testFgets()
{
    char name[SIZE];

    printf("Enter donor name: ");

    if (fgets(name, sizeof(name), stdin) != NULL)
    {
        name[strcspn(name, "\n")] = '\0';

        printf("Donor name: %s\n", name);
        printf("Characters stored: %zu\n", strlen(name));
    }
}

/* Test 3: File error handling */
void testFile(const char *filename)
{
    FILE *fp = fopen(filename, "r");

    if (fp == NULL)
    {
        perror("File opening error");
        return;
    }

    printf("File opened successfully\n");

    fclose(fp);
}

/* Test 4: Memory allocation and release */
void testMemory()
{
    int *p = malloc(5 * sizeof(int));

    if (p == NULL)
    {
        printf("Memory allocation failed\n");
        return;
    }

    for (int i = 0; i < 5; i++)
        p[i] = (i + 1) * 10;

    printf("Memory values: ");

    for (int i = 0; i < 5; i++)
        printf("%d ", p[i]);

    printf("\n");

    free(p);

    p = NULL;

    printf("Memory released successfully\n");
}

int main(int argc, char *argv[])
{
    if (argc < 2)
    {
        printf("Usage:\n");
        printf("./b2 scanf\n");
        printf("./b2 fgets\n");
        printf("./b2 file filename\n");
        printf("./b2 find GROUP\n");
        printf("./b2 issue GROUP UNITS\n");
        printf("./b2 memory\n");

        return 1;
    }

    if (strcmp(argv[1], "scanf") == 0 && argc == 2)
    {
        testScanf();
    }
    else if (strcmp(argv[1], "fgets") == 0 && argc == 2)
    {
        testFgets();
    }
    else if (strcmp(argv[1], "file") == 0 && argc == 3)
    {
        testFile(argv[2]);
    }
    else if (strcmp(argv[1], "find") == 0 && argc == 3)
    {
        if (validGroup(argv[2]))
            printf("Searching blood group %s\n", argv[2]);
        else
            printf("Invalid blood group\n");
    }
    else if (strcmp(argv[1], "issue") == 0 && argc == 4)
    {
        char *end;
        long units = strtol(argv[3], &end, 10);

        if (!validGroup(argv[2]))
            printf("Invalid blood group\n");
        else if (*argv[3] == '\0' || *end != '\0' ||
                 units <= 0 || units > 100000)
            printf("Invalid number of units\n");
        else
            printf("Issuing %ld units of %s\n",
                   units, argv[2]);
    }
    else if (strcmp(argv[1], "memory") == 0 && argc == 2)
    {
        testMemory();
    }
    else
    {
        printf("Invalid command or arguments\n");
        return 1;
    }

    return 0;
}
