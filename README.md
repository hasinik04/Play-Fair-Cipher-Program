## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER


## AIM:
 

To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




## Program:
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define SIZE 100

// Convert string to lowercase
void toLowerCase(char text[], int length) {
    for (int i = 0; i < length; i++) {
        text[i] = tolower(text[i]);
    }
}

// Remove spaces from string
int removeSpaces(char* text, int length) {
    int count = 0;
    for (int i = 0; i < length; i++) {
        if (text[i] != ' ') {
            text[count++] = text[i];
        }
    }
    text[count] = '\0'; // Null terminate
    return count;
}

// Generate 5x5 key square
void generateKeyTable(char key[], int ks, char keyT[5][5]) {
    int dicty[26] = {0};
    int i, j, k;

    // Mark used letters (excluding 'j')
    for (i = 0; i < ks; i++) {
        if (key[i] != 'j') {
            dicty[key[i] - 'a'] = 1;
        }
    }
    dicty['j' - 'a'] = 1; // Mark 'j' manually

    i = 0, j = 0;
    
    // Fill key square with key
    for (k = 0; k < ks; k++) {
        if (dicty[key[k] - 'a'] == 1) {
            dicty[key[k] - 'a'] = 2;
            keyT[i][j++] = key[k];
            if (j == 5) {
                i++; j = 0;
            }
        }
    }

    // Fill remaining slots with unused letters
    for (k = 0; k < 26; k++) {
        if (dicty[k] == 0 && k != ('j' - 'a')) {
            keyT[i][j++] = k + 'a';
            if (j == 5) {
                i++; j = 0;
            }
        }
    }
}

// Find positions of two characters in key square
void search(char keyT[5][5], char a, char b, int pos[]) {
    if (a == 'j') a = 'i';
    if (b == 'j') b = 'i';

    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (keyT[i][j] == a) {
                pos[0] = i;
                pos[1] = j;
            }
            if (keyT[i][j] == b) {
                pos[2] = i;
                pos[3] = j;
            }
        }
    }
}

// Modulo 5 function
int mod5(int num) {
    return (num % 5);
}

// Ensure even-length plaintext
int prepareText(char str[], int length) {
    if (length % 2 != 0) {
        str[length++] = 'x'; // Use 'x' instead of 'z'
        str[length] = '\0';
    }
    return length;
}

// Encrypt text using Playfair cipher
void encrypt(char str[], char keyT[5][5], int length) {
    int pos[4];

    for (int i = 0; i < length; i += 2) {
        search(keyT, str[i], str[i + 1], pos);

        if (pos[0] == pos[2]) { // Same row
            str[i] = keyT[pos[0]][mod5(pos[1] + 1)];
            str[i + 1] = keyT[pos[0]][mod5(pos[3] + 1)];
        } else if (pos[1] == pos[3]) { // Same column
            str[i] = keyT[mod5(pos[0] + 1)][pos[1]];
            str[i + 1] = keyT[mod5(pos[2] + 1)][pos[1]];
        } else { // Rectangle swap
            str[i] = keyT[pos[0]][pos[3]];
            str[i + 1] = keyT[pos[2]][pos[1]];
        }
    }
}

// Main encryption function
void encryptByPlayfairCipher(char str[], char key[]) {
    int keyLen = strlen(key);
    int textLen = strlen(str);
    char keyT[5][5];

    // Process key
    keyLen = removeSpaces(key, keyLen);
    toLowerCase(key, keyLen);

    // Process plaintext
    toLowerCase(str, textLen);
    textLen = removeSpaces(str, textLen);
    textLen = prepareText(str, textLen);

    // Generate key table and encrypt
    generateKeyTable(key, keyLen, keyT);
    encrypt(str, keyT, textLen);
}

// Main function
int main() {
    char str[SIZE], key[SIZE];

    // Ask user for key
    printf("Enter key: ");
    fgets(key, SIZE, stdin);
    key[strcspn(key, "\n")] = '\0'; // Remove trailing newline

    // Ask user for plaintext
    printf("Enter plaintext: ");
    fgets(str, SIZE, stdin);
    str[strcspn(str, "\n")] = '\0'; // Remove trailing newline

    encryptByPlayfairCipher(str, key);

    printf("Cipher text: %s\n", str);

    return 0;
}

```



## Output:
![image](https://github.com/user-attachments/assets/4db010df-127e-4ec9-9eae-e9fc252b4f1a)
