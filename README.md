#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main() {
    pid_t parent_pid = getpid();
    printf("Parent process PID: %d\n", parent_pid);

    // Parent process creates child process
    pid_t child_pid = fork();

    if (child_pid < 0) {
        // Fork failed
        fprintf(stderr, "Fork failed\n");
        return 1;
    } else if (child_pid == 0) {
        // Child process

        pid_t sub_child_pid = fork();

        if (sub_child_pid < 0) {
            // Fork failed
            fprintf(stderr, "Fork failed\n");
            return 1;
        } else if (sub_child_pid == 0) {
            // Sub-child process
            sleep(10); // Wait for a few seconds
            printf("Sub-child process (Orphan) PID: %d\n", getpid());
            printf("Parent PID of Sub-child: %d\n", getppid());
        } else {
            // Child process
            printf("Child process PID: %d\n", getpid());
            sleep(1); // Wait for a moment to ensure parent process waits
            exit(0);
        }
    } else {
        // Parent process
        sleep(5); // Wait for child to become zombie
        int status;
        waitpid(child_pid, &status, 0);
        printf("Child process has terminated.\n");
    }

    return 0;
}
