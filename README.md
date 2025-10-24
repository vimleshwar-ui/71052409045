# 71052409045
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Feedback {
    char studentName[50];
    char subject[50];
    int rating;
    char comments[200];
};

void addFeedback();
void viewFeedback();
void searchFeedback();

int main() {
    int choice;
    while (1) {
        printf("\n========== Student Feedback Tracking System ==========\n");
        printf("1. Add Feedback\n");
        printf("2. View All Feedback\n");
        printf("3. Search Feedback\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        getchar(); // consume newline

        switch (choice) {
            case 1:
                addFeedback();
                break;
            case 2:
                viewFeedback();
                break;
            case 3:
                searchFeedback();
                break;
            case 4:
                printf("Exiting... Thank you!\n");
                exit(0);
            default:
                printf("Invalid choice! Try again.\n");
        }
    }
    return 0;
}

void addFeedback() {
    FILE *fp = fopen("feedback.txt", "a");
    if (fp == NULL) {
        printf("Error opening file!\n");
        return;
    }

    struct Feedback f;

    printf("Enter Student Name: ");
    fgets(f.studentName, sizeof(f.studentName), stdin);
    f.studentName[strcspn(f.studentName, "\n")] = 0; // remove newline

    printf("Enter Subject: ");
    fgets(f.subject, sizeof(f.subject), stdin);
    f.subject[strcspn(f.subject, "\n")] = 0;

    printf("Enter Rating (1-5): ");
    scanf("%d", &f.rating);
    getchar();

    printf("Enter Comments: ");
    fgets(f.comments, sizeof(f.comments), stdin);
    f.comments[strcspn(f.comments, "\n")] = 0;

    fprintf(fp, "%s|%s|%d|%s\n", f.studentName, f.subject, f.rating, f.comments);
    fclose(fp);

    printf("Feedback submitted successfully!\n");
}

void viewFeedback() {
    FILE *fp = fopen("feedback.txt", "r");
    if (fp == NULL) {
        printf("No feedback found.\n");
        return;
    }

    struct Feedback f;
    char line[400];
    printf("\n--- All Feedback Entries ---\n");
    while (fgets(line, sizeof(line), fp)) {
        sscanf(line, "%[^|]|%[^|]|%d|%[^\n]", f.studentName, f.subject, &f.rating, f.comments);
        printf("\nStudent: %s\nSubject: %s\nRating: %d\nComments: %s\n", f.studentName, f.subject, f.rating, f.comments);
        printf("---------------------------------------------\n");
    }
    fclose(fp);
}

void searchFeedback() {
    FILE *fp = fopen("feedback.txt", "r");
    if (fp == NULL) {
        printf("No feedback found.\n");
        return;
    }

    char searchTerm[50];
    printf("Enter student name or subject to search: ");
    fgets(searchTerm, sizeof(searchTerm), stdin);
    searchTerm[strcspn(searchTerm, "\n")] = 0;

    struct Feedback f;
    char line[400];
    int found = 0;

    while (fgets(line, sizeof(line), fp)) {
        sscanf(line, "%[^|]|%[^|]|%d|%[^\n]", f.studentName, f.subject, &f.rating, f.comments);
        if (strstr(f.studentName, searchTerm) || strstr(f.subject, searchTerm)) {
            printf("\nStudent: %s\nSubject: %s\nRating: %d\nComments: %s\n", f.studentName, f.subject, f.rating, f.comments);
            printf("---------------------------------------------\n");
            found = 1;
        }
    }

    if (!found)
        printf("No matching feedback found.\n");

    fclose(fp);
}
