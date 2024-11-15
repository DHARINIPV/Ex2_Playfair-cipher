# PlayFair Cipher
Playfair Cipher using with different key values

## AIM:
To implement a program to encrypt a plain text and decrypt a cipher text using play fair Cipher substitution technique.

## DESIGN STEPS:
Step 1:
Design of PlayFair Cipher algorithnm

Step 2:
Implementation using C or pyhton code

Step 3:
Testing algorithm with different key values.

## ALGORITHM DESCRIPTION: 
The Playfair cipher uses a 5 by 5 table containing a key word or phrase. To generate the key table, first fill the spaces in the table with the letters of the keyword, then fill the remaining spaces with the rest of the letters of the alphabet in order (usually omitting "Q" to reduce the alphabet to fit; other versions put both "I" and "J" in the same space). The key can be written in the top rows of the table, from left to right, or in some other pattern, such as a spiral beginning in the upper-left-hand corner and ending in the centre. The keyword together with the conventions for filling in the 5 by 5 table constitutes the cipher key. To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. Then apply the following 4 rules, to each pair of letters in the plaintext:

If both letters are the same (or only one letter is left), add an "X" after the first letter. Encrypt the new pair and continue. Some
variants of Playfair use "Q" instead of "X", but any letter, itself uncommon as a repeated pair, will do.
If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively (wrapping around to the left side of the row if a letter in the original pair was on the right side of the row).
If the letters appear on the same column of your table, replace them with the letters immediately below respectively (wrapping around to the top side of the column if a letter in the original pair was on the bottom side of the column).
If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair. The order is important – the first letter of the encrypted pair is the one that lies on the same row as the first letter of the plaintext pair. To decrypt, use the INVERSE (opposite) of the last 3 rules, and the 1st as-is (dropping any extra "X"s, or "Q"s that do not make sense in the final message when finished).

## PROGRAM:
```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 5

// Function prototypes
void generateKeyTable(char key[], char keyTable[SIZE][SIZE]);
void processMessage(char message[], char processedMessage[]);
void search(char keyTable[SIZE][SIZE], char a, char b, int pos[]);
void encrypt(char message[], char keyTable[SIZE][SIZE], char encryptedMessage[]);
void decrypt(char encryptedMessage[], char keyTable[SIZE][SIZE], char decryptedMessage[]);

int main() {
    char key[SIZE * SIZE], message[100];
    char keyTable[SIZE][SIZE];
    char encryptedMessage[100], decryptedMessage[100];

    // Input the key and message
    printf("Enter the key: ");
    fgets(key, sizeof(key), stdin);
    key[strcspn(key, "\n")] = '\0'; // Remove newline character

    printf("Enter the message: ");
    fgets(message, sizeof(message), stdin);
    message[strcspn(message, "\n")] = '\0'; // Remove newline character

    // Generate key table
    generateKeyTable(key, keyTable);

    // Encrypt the message
    processMessage(message, message); // Format message for encryption
    encrypt(message, keyTable, encryptedMessage);
    printf("Encrypted message: %s\n", encryptedMessage);

    // Decrypt the message
    decrypt(encryptedMessage, keyTable, decryptedMessage);
    printf("Decrypted message: %s\n", decryptedMessage);

    return 0;
}

// Function to generate the key table from the key
void generateKeyTable(char key[], char keyTable[SIZE][SIZE]) {
    int i, j, k = 0, alpha[26] = {0};
    char filteredKey[SIZE * SIZE] = "";

    // Filter the key and remove duplicates
    for (i = 0; i < strlen(key); i++) {
        if (isalpha(key[i])) {
            char letter = toupper(key[i]);
            if (letter == 'J') letter = 'I'; // Replace J with I
            if (alpha[letter - 'A'] == 0) {
                filteredKey[k++] = letter;
                alpha[letter - 'A'] = 1;
            }
        }
    }

    // Fill the key table with the filtered key
    k = 0;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            if (k < strlen(filteredKey)) {
                keyTable[i][j] = filteredKey[k++];
            } else {
                for (char letter = 'A'; letter <= 'Z'; letter++) {
                    if (letter == 'J') continue;
                    if (alpha[letter - 'A'] == 0) {
                        keyTable[i][j] = letter;
                        alpha[letter - 'A'] = 1;
                        break;
                    }
                }
            }
        }
    }
}

// Function to process the message: format pairs and handle odd lengths
void processMessage(char message[], char processedMessage[]) {
    int i, j = 0;
    for (i = 0; i < strlen(message); i++) {
        if (isalpha(message[i])) {
            processedMessage[j++] = toupper(message[i]);
        }
    }
    processedMessage[j] = '\0';

    for (i = 0; i < j; i += 2) {
        if (processedMessage[i] == processedMessage[i + 1]) {
            memmove(&processedMessage[i + 1], &processedMessage[i], j - i + 1);
            processedMessage[i + 1] = 'X'; // Insert 'X' between duplicate letters
            j++;
        }
    }
    if (j % 2 != 0) {
        processedMessage[j++] = 'X'; // Pad with 'X' if odd length
    }
    processedMessage[j] = '\0';
}

// Function to find the position of characters in the key table
void search(char keyTable[SIZE][SIZE], char a, char b, int pos[]) {
    int i, j;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            if (keyTable[i][j] == a) {
                pos[0] = i;
                pos[1] = j;
            }
            if (keyTable[i][j] == b) {
                pos[2] = i;
                pos[3] = j;
            }
        }
    }
}

// Function to encrypt the message
void encrypt(char message[], char keyTable[SIZE][SIZE], char encryptedMessage[]) {
    int i, pos[4];
    for (i = 0; i < strlen(message); i += 2) {
        search(keyTable, message[i], message[i + 1], pos);

        if (pos[0] == pos[2]) { // Same row
            encryptedMessage[i] = keyTable[pos[0]][(pos[1] + 1) % SIZE];
            encryptedMessage[i + 1] = keyTable[pos[0]][(pos[3] + 1) % SIZE];
        } else if (pos[1] == pos[3]) { // Same column
            encryptedMessage[i] = keyTable[(pos[0] + 1) % SIZE][pos[1]];
            encryptedMessage[i + 1] = keyTable[(pos[2] + 1) % SIZE][pos[1]];
        } else { // Rectangle swap
            encryptedMessage[i] = keyTable[pos[0]][pos[3]];
            encryptedMessage[i + 1] = keyTable[pos[2]][pos[1]];
        }
    }
    encryptedMessage[i] = '\0';
}

// Function to decrypt the message
void decrypt(char encryptedMessage[], char keyTable[SIZE][SIZE], char decryptedMessage[]) {
    int i, pos[4];
    for (i = 0; i < strlen(encryptedMessage); i += 2) {
        search(keyTable, encryptedMessage[i], encryptedMessage[i + 1], pos);

        if (pos[0] == pos[2]) { // Same row
            decryptedMessage[i] = keyTable[pos[0]][(pos[1] - 1 + SIZE) % SIZE];
            decryptedMessage[i + 1] = keyTable[pos[0]][(pos[3] - 1 + SIZE) % SIZE];
        } else if (pos[1] == pos[3]) { // Same column
            decryptedMessage[i] = keyTable[(pos[0] - 1 + SIZE) % SIZE][pos[1]];
            decryptedMessage[i + 1] = keyTable[(pos[2] - 1 + SIZE) % SIZE][pos[1]];
        } else { // Rectangle swap
            decryptedMessage[i] = keyTable[pos[0]][pos[3]];
            decryptedMessage[i + 1] = keyTable[pos[2]][pos[1]];
        }
    }
    decryptedMessage[i] = '\0';
}
```

## OUTPUT:
![image](https://github.com/user-attachments/assets/793b32f2-370f-4bff-8e85-a550fd668787)

## RESULT:
The program is executed successfully
