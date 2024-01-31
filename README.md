//# Project---Task-Management-System
/*This C program implements a simple Task Management System. Users can add tasks, mark tasks as done, display the task list, and save/load tasks from a file. The tasks are stored in a linked list, providing a basic yet functional task management solution. The program is designed for simplicity and ease of use.*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Task
{
    int taskId;
    char taskName[100];
    int done;
    struct Task* next;
};

struct Task* createTask(const char* taskName)
{
    struct Task* newTask = (struct Task*)malloc(sizeof(struct Task));
    if (newTask != NULL) {
        static int taskIdCounter = 1;
        newTask->taskId = taskIdCounter++;
        strcpy(newTask->taskName, taskName);
        newTask->done = 0;
        newTask->next = NULL;
    }
    return newTask;
}

void addTask(struct Task** head, const char* taskName)
{
    struct Task* newTask = createTask(taskName);
    struct Task **p = head;
    if (newTask != NULL) {
        while (*p != NULL) {
            p = &((*p)->next);
        }
        *p = newTask;
        printf("Task added: %s\n", taskName);
    } else {
        printf("Failed to create task.\n");
    }
}

void markDone(struct Task* head, const char* taskName) {
    while (head != NULL) {
        if (strcmp(head->taskName, taskName) == 0) {
            head->done = 1;
            printf("Task marked as done: %s\n", taskName);
            return;
        }
        head = head->next;
    }
    printf("Task not found: %s\n", taskName);
}

void displayTasks(struct Task* head) {
    printf("Task List:\n");
    while (head != NULL) {
        printf("[%d][%s] %s\n", head->taskId, (head->done ? "X" : " "), head->taskName);
        head = head->next;
    }
}

void freeList(struct Task* head) {
    struct Task* current = head;
    struct Task* next;
    while (current != NULL) {
        next = current->next;
        free(current);
        current = next;
    }
}

void saveTasks(struct Task* head) {
    FILE* file = fopen("tasks.txt", "w");
    if (file != NULL) {
        while (head != NULL) {
            fprintf(file, "%d %d %s\n", head->taskId, head->done, head->taskName);
            head = head->next;
        }
        fclose(file);
        printf("Tasks saved to file.\n");
    } else {
        printf("Failed to open file for writing.\n");
    }
}

void loadTasks(struct Task** head) {
    FILE* file = fopen("tasks.txt", "r");
    if (file != NULL) {
        struct Task newTask;
        while (fscanf(file, "%d %d %[^\n]s", &newTask.taskId, &newTask.done, newTask.taskName) == 3) {
            newTask.next = NULL;
            addTask(head, newTask.taskName);
        }
        fclose(file);
        printf("Tasks loaded from file.\n");
    } else {
        printf("No existing file found. Starting with an empty task list.\n");
    }
}


int main() {
    struct Task* taskList = NULL;
    int choice;
    char taskName[100];
    loadTasks(&taskList);
    do {
        printf("\nTask Management System\n");
        printf("1. Add Task\n");
        printf("2. Mark Task as Done\n");
        printf("3. Display Tasks\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1:
                printf("Enter task name: ");
                scanf(" %[^\n]s", taskName);
                addTask(&taskList, taskName);
                break;
            case 2:
                printf("Enter task name to mark as done: ");
                scanf(" %[^\n]s", taskName);
                markDone(taskList, taskName);
                break;
            case 3:
                displayTasks(taskList);
                break;
            case 4:
                printf("Exiting program.\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 4);
    saveTasks(taskList);
    freeList(taskList);
    return 0;
}
