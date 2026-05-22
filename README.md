# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    long long x, y;
} Point;

/* Function to find modular inverse */
long long modInverse(long long a, long long m) {
    a = a % m;

    for (long long x = 1; x < m; x++) {
        if ((a * x) % m == 1)
            return x;
    }

    return -1; // Inverse does not exist
}

/* Point Addition on Elliptic Curve */
Point add(Point P, Point Q, long long a, long long p) {
    Point R;
    long long lambda, numerator, denominator, inverse;

    /* Point Doubling */
    if (P.x == Q.x && P.y == Q.y) {

        numerator = (3 * P.x * P.x + a) % p;
        denominator = (2 * P.y) % p;

        inverse = modInverse(denominator, p);

        if (inverse == -1) {
            printf("Modular inverse does not exist.\n");
            exit(1);
        }

        lambda = (numerator * inverse) % p;
    }

    /* Point Addition */
    else {

        numerator = (Q.y - P.y + p) % p;
        denominator = (Q.x - P.x + p) % p;

        inverse = modInverse(denominator, p);

        if (inverse == -1) {
            printf("Modular inverse does not exist.\n");
            exit(1);
        }

        lambda = (numerator * inverse) % p;
    }

    R.x = (lambda * lambda - P.x - Q.x) % p;
    R.y = (lambda * (P.x - R.x) - P.y) % p;

    if (R.x < 0)
        R.x += p;

    if (R.y < 0)
        R.y += p;

    return R;
}

/* Scalar Multiplication */
Point multiply(Point P, long long k, long long a, long long p) {

    Point R = P;

    for (int i = 1; i < k; i++) {
        R = add(R, P, a, p);
    }

    return R;
}

int main() {

    long long p, a, b;
    long long privateA, privateB;

    Point G;
    Point publicA, publicB;
    Point sharedA, sharedB;

    printf("Enter the prime number (p): ");
    scanf("%lld", &p);

    printf("Enter curve parameters a and b: ");
    scanf("%lld %lld", &a, &b);

    printf("Enter base point G (x y): ");
    scanf("%lld %lld", &G.x, &G.y);

    printf("Enter Alice private key: ");
    scanf("%lld", &privateA);

    printf("Enter Bob private key: ");
    scanf("%lld", &privateB);

    /* Generate Public Keys */
    publicA = multiply(G, privateA, a, p);
    publicB = multiply(G, privateB, a, p);

    printf("\nAlice Public Key = (%lld, %lld)\n",
           publicA.x, publicA.y);

    printf("Bob Public Key = (%lld, %lld)\n",
           publicB.x, publicB.y);

    /* Shared Secret */
    sharedA = multiply(publicB, privateA, a, p);
    sharedB = multiply(publicA, privateB, a, p);

    printf("\nShared Secret computed by Alice = (%lld, %lld)\n",
           sharedA.x, sharedA.y);

    printf("Shared Secret computed by Bob = (%lld, %lld)\n",
           sharedB.x, sharedB.y);

    if (sharedA.x == sharedB.x &&
        sharedA.y == sharedB.y)

        printf("\nKey Exchange Successful\n");

    else
        printf("\nKey Exchange Failed\n");

    return 0;
}
```


## Output:
<img width="1919" height="964" alt="image" src="https://github.com/user-attachments/assets/136267f6-08e6-4d98-89e8-b106ee11b21d" />


## Result:
The program is executed successfully

