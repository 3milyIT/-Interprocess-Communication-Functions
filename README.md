# -Interprocess-Communication-Functions

Title: Interprocess Communication Functions for Seamless Collaboration on GitHub

Introduction:
Effective interprocess communication (IPC) is vital for facilitating seamless collaboration among developers on platforms like GitHub. To enhance communication and coordination among contributors, several IPC functions are commonly used. In this short article, we will highlight a few essential functions for interprocess communication, focusing on their relevance and benefits in the GitHub collaboration workflow.

1. Messaging Functions:
Messaging functions enable real-time communication and instant message exchange among developers. These functions provide a convenient way for contributors to discuss code-related matters, ask questions, and share ideas without leaving the GitHub platform. Examples of messaging functions include:

- `send_message(user, message)`: Sends a message to a specific user on GitHub, allowing direct communication between collaborators.
- `broadcast_message(message)`: Sends a message to all team members or participants involved in the GitHub repository, ensuring everyone stays informed about important updates or discussions.

By incorporating messaging functions into the GitHub workflow, developers can foster a more collaborative and efficient environment, eliminating the need for external communication channels.

2. Event Notification Functions:
Event notification functions are crucial for keeping contributors informed about important events or changes in the repository. These functions notify team members in real-time, ensuring prompt responses and timely action. Some commonly used event notification functions include:

- `notify_pull_request(pull_request)`: Notifies relevant team members about a new pull request, allowing for immediate review and feedback.
- `notify_code_review_request(review_request)`: Sends a notification to designated reviewers, requesting their input on a particular code change.

These functions enable seamless coordination and ensure that all contributors are aware of critical updates or requests, minimizing delays in the development process.

3. Conflict Resolution Functions:
Collaborative development often involves concurrent modifications to shared code, leading to potential conflicts. Conflict resolution functions assist in identifying and resolving these conflicts effectively. Some useful conflict resolution functions include:

- `resolve_conflict(conflict)`: Provides a mechanism for identifying and resolving code conflicts in a collaborative context.
- `automerge_changes(branch, changes)`: Automatically merges code changes from different branches, reducing the manual effort required for conflict resolution.

By integrating conflict resolution functions into the GitHub workflow, developers can streamline the collaboration process and maintain code integrity even in complex scenarios.

Conclusion:
Interprocess communication functions play a vital role in enabling seamless collaboration among developers on GitHub. By leveraging messaging functions, event notification functions, and conflict resolution functions, contributors can enhance communication, coordination, and code integrity. These functions promote efficient collaboration, minimizing delays, and improving the overall development experience on GitHub.


Interprocess communication (IPC) is crucial for enabling efficient data exchange and synchronization between different processes. In this short article, we will explore three essential IPC mechanisms: pipe, shmat, and msgsnd. These functions, widely used in various operating systems and programming languages, provide reliable means for interprocess communication. Understanding their capabilities and usage can greatly enhance communication and collaboration among processes.

Pipe:
The pipe function facilitates unidirectional communication between two related processes. It creates a pipe, which is a unidirectional data channel, allowing one process to write data to the pipe while the other process reads from it. Key functions associated with pipes include:
pipe(fd): Creates a pipe and provides two file descriptors (fd[0] for reading and fd[1] for writing) to the calling process.
write(fd[1], data, size): Writes data to the pipe using the file descriptor associated with the writing end.
read(fd[0], buffer, size): Reads data from the pipe using the file descriptor associated with the reading end.
Pipes are commonly used for communication between related processes, such as a parent process and its child processes.

shmat:
The shmat function, short for "shared memory attach," enables processes to share a common memory segment. This mechanism allows multiple processes to access and modify the same memory space simultaneously. Key functions associated with shared memory include:
shmget(key, size, flags): Creates or opens a shared memory segment identified by a key, specifying its size and flags.
shmat(shmid, addr, flags): Attaches the shared memory segment identified by shmid to the calling process's address space and returns a pointer to the attached memory.
Processes can then read from and write to the shared memory region using the obtained pointer, enabling efficient and fast data sharing between processes.

msgsnd:
The msgsnd function facilitates communication between processes through message queues. It allows processes to send messages to a message queue, where they can be read by other processes. Key functions associated with message queues include:
msgget(key, flags): Creates or accesses a message queue identified by a key, specifying the flags.
msgsnd(msqid, msgp, size, flags): Sends a message to the message queue identified by msqid, with the message contents specified by msgp and size.
msgrcv(msqid, msgp, size, type, flags): Receives a message from the message queue identified by msqid and stores it in the buffer pointed to by msgp.
Message queues provide a flexible and reliable mechanism for interprocess communication, allowing processes to exchange structured messages efficiently.






.




## EXERCISE 1
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define ARRAY_SIZE 10

// Function to compare elements in the sorting function
int compare(const void* a, const void* b) {
    return (*(int*)a - *(int*)b);
}

// Function for generating, sorting, and displaying digits
void* generateAndSort(void* arg) {
    int numbers[ARRAY_SIZE];

    // Generating 10 random digits from 0 to 100
    for (int i = 0; i < ARRAY_SIZE; i++) {
        numbers[i] = rand() % 101;
    }

    // Sorting the sequence of digits
    qsort(numbers, ARRAY_SIZE, sizeof(int), compare);

    // Displaying the sorted sequence of digits
    printf("Sorted sequence of digits: ");
    for (int i = 0; i < ARRAY_SIZE; i++) {
        printf("%d ", numbers[i]);
    }
    printf("\n");

    pthread_exit(NULL);
}

int main() {
    pthread_t thread;

    // Initializing the random number generator
    srand(time(NULL));

    // Creating and running the thread
    if (pthread_create(&thread, NULL, generateAndSort, NULL) != 0) {
        fprintf(stderr, "Error creating thread.\n");
        return 1;
    }

    // Waiting for the thread to finish
    if (pthread_join(thread, NULL) != 0) {
        fprintf(stderr, "Error waiting for thread completion.\n");
        return 1;
    }

    return 0;
}


# EXERCISE 2
## SENDER 
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

#define MAX_MESSAGE_SIZE 100

// Definicja struktury wiadomości
struct message {
    long message_type;
    char text[MAX_MESSAGE_SIZE];
};

int main() {
    key_t key;
    int message_queue_id;
    struct message msg;

    // Generowanie klucza dla kolejki komunikatów
    key = ftok("sender.c", 'A');

    // Tworzenie kolejki komunikatów
    message_queue_id = msgget(key, IPC_CREAT | 0666);
    if (message_queue_id == -1) {
        perror("msgget");
        exit(1);
    }

    // Wysyłanie wiadomości naprzemiennie
    strcpy(msg.text, "zalicz");
    msg.message_type = 1;
    msgsnd(message_queue_id, &msg, sizeof(struct message), IPC_NOWAIT);

    strcpy(msg.text, "kolosa");
    msg.message_type = 1;
    msgsnd(message_queue_id, &msg, sizeof(struct message), IPC_NOWAIT);

    return 0;
}
```
# RECEIVER 
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

#define MAX_MESSAGE_SIZE 100

// Definicja struktury wiadomości
struct message {
    long message_type;
    char text[MAX_MESSAGE_SIZE];
};

int main() {
    key_t key;
    int message_queue_id;
    struct message msg;

    // Generowanie klucza dla kolejki komunikatów
    key = ftok("sender.c", 'A');

    // Uzyskiwanie dostępu do kolejki komunikatów
    message_queue_id = msgget(key, 0666);
    if (message_queue_id == -1) {
        perror("msgget");
        exit(1);
    }

    // Odbieranie i wyświetlanie wiadomości
    while (1) {
        msgrcv(message_queue_id, &msg, sizeof(struct message), 1, 0);
        printf("Otrzymano wiadomość: %s\n", msg.text);
    }

    return 0;
}
```

# EXERCISE 3
https://docs.docker.com/get-started/02_our_app/
https://docs.docker.com/compose/gettingstarted/

Poniżej znajduje się przykładowy plik Dockerfile, który tworzy obraz na podstawie Pythona 3.9, ustawia zmienną środowiskową TESTSCORE i instaluje dodatkową paczkę `wget` dla systemu Linux:

```dockerfile
FROM python:3.9

# Ustawianie zmiennej środowiskowej TESTSCORE
ENV TESTSCORE 42

# Instalowanie paczki wget
RUN apt-get update && apt-get install -y wget

# Dodatkowe instrukcje
# ...

# Uruchamianie aplikacji
CMD [ "python" ]
```

Powyższy Dockerfile tworzy obraz na podstawie oficjalnego obrazu Pythona 3.9. Następnie ustawia zmienną środowiskową TESTSCORE na wartość 42 za pomocą dyrektywy `ENV`. Kolejna instrukcja `RUN` aktualizuje repozytoria apt-get i instaluje pakiet `wget` przy użyciu polecenia systemowego. Możesz dodawać dodatkowe instrukcje, takie jak instalacja innych pakietów czy kopiowanie plików do obrazu, w odpowiednich miejscach.

Po napisaniu Dockerfile'a, można zbudować obraz, wykonując polecenie `docker build` w katalogu zawierającym plik Dockerfile:

```bash
docker build -t nazwa_obrazu .
```

W powyższym poleceniu `nazwa_obrazu` to dowolna nazwa, którą chcesz nadać obrazowi. Po zakończeniu budowania obrazu, będzie można uruchomić kontener na jego podstawie i sprawdzić zmienną środowiskową TESTSCORE oraz zainstalowaną paczkę `wget`.

# EXERCISE 4
Oto przykładowy plik `docker-compose.yml`, który składa się z dwóch serwisów: `web` (oparty na wcześniejszym Dockerfile'u) oraz `db` (PostgreSQL 12.11). W konfiguracji dodano statyczną adresację IP dla obu serwisów i zapewniono, że `web` nie będzie uruchomiony, dopóki `db` nie będzie gotowe:

```yaml
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    depends_on:
      - db
    networks:
      mynetwork:
        ipv4_address: 172.20.0.2

  db:
    image: postgres:12.11
    networks:
      mynetwork:
        ipv4_address: 172.20.0.3

networks:
  mynetwork:
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

W powyższym pliku `docker-compose.yml` zdefiniowano dwa serwisy: `web` i `db`. Serwis `web` jest budowany na podstawie wcześniejszego Dockerfile'u. Klauzula `depends_on` określa, że `web` zależy od `db`, co oznacza, że `db` musi być uruchomione przed `web`. Następnie zdefiniowano sieć `mynetwork` i przypisano statyczne adresy IP dla obu serwisów (`172.20.0.2` dla `web` i `172.20.0.3` dla `db`).

Aby uruchomić kontenery na podstawie pliku `docker-compose.yml`, przejdź do katalogu zawierającego ten plik i wykonaj polecenie:

```bash
docker-compose up
```

Docker Compose zbuduje i uruchomi oba serwisy, zapewniając, że `db` zostanie uruchomione przed `web`. Możesz dostosować plik `docker-compose.yml` według własnych potrzeb, na przykład zmieniając nazwy serwisów, dodając dodatkowe konfiguracje itp.
