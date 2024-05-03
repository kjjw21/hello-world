# hello-world
GitHub를 알아가는 단계로 연습용입니다.

이번에 객체지향프로그래밍 프로젝트로 선형대수학의 행렬변환과 관련된 코드를 작성했다.

#include <iostream>
#include <iomanip>
#include <cmath>
using namespace std;

// print the matrix, use a window size of 3 and right align
#include <cmath> // for modf function

void printMatrix(double** matrix, int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n + 1; j++) {
            double intPart;
            if (fabs(matrix[i][j]) < 1e-9) {  // 0에 근접한 값은 0으로 처리
                cout << setw(8) << "0 ";
            } else if (modf(matrix[i][j], &intPart) == 0.0) { // 소수 부분이 0이면 정수로 출력
                cout << setw(8) << static_cast<int>(matrix[i][j]) << " ";
            } else { // 소수 부분이 있으면 소수로 출력
                cout << setw(8) << fixed << setprecision(6) << matrix[i][j] << " ";
            }
        }
        cout << endl;
    }
    cout << endl;
}



// Swap two rows of a matrix
void swapRows(double** matrix, int row1, int row2, int n) {
    // 각 열에 대해 두 행의 원소를 교환
    for (int i = 0; i <= n; i++) {
        swap(matrix[row1][i], matrix[row2][i]);
    }
}



void performGaussianElimination(double** matrix, int n) {
    for (int i = 0; i < n; i++) {
        // 대각 성분이 0인 경우 교환
        if (fabs(matrix[i][i]) < 1e-9) {
            for (int j = i + 1; j < n; j++) {
                if (fabs(matrix[j][i]) > 1e-9) {
                    swapRows(matrix, i, j, n);
                    break;
                }
            }
        }
        // 현재 행을 기준으로 다른 모든 행을 0으로 만듬
        for (int j = 0; j < n; j++) {
            if (i != j) {
                double ratio = matrix[j][i] / matrix[i][i];
                for (int k = i; k <= n; k++) {
                    matrix[j][k] -= ratio * matrix[i][k];
                }
            }
        }
    }

    cout << "Gaussian Elimination result: the reduced row echelon form (RREF) matrix is:" << endl;
    printMatrix(matrix, n);
}


void backSubstitution(double** matrix, int n, double* solution) {
    // 역순으로 각 행을 처리
    for (int i = n - 1; i >= 0; i--) {
        solution[i] = matrix[i][n] / matrix[i][i];
        // 위의 행들에서 i번째 변수에 해당하는 값을 제거
        for (int j = i - 1; j >= 0; j--) {
            matrix[j][n] -= matrix[j][i] * solution[i];
        }
    }
}


void solveSystem(double** matrix, int n) {
    performGaussianElimination(matrix, n);
    double* solution = new double[n];
    backSubstitution(matrix, n, solution);

    std::cout << "The solution to the system of linear equations is:" << std::endl;
    for (int i = 0; i < n; i++) {
        if (fabs(solution[i]) < 1e-9) { 
            std::cout << "x[" << i << "] = 0" << std::endl; 
        } else {
            double intPart;
            if (modf(solution[i], &intPart) == 0.0) {
                std::cout << "x[" << i << "] = " << static_cast<int>(intPart) << std::endl; 
            } else {
                std::cout << "x[" << i << "] = " << std::fixed << std::setprecision(6) << solution[i] << std::endl; 
            }
        }
    }
    std::cout << std::endl;
    delete[] solution;
}


int main() {
    // create the augmented matrix
    int n = 3; // size of the matrix
    double** A = new double*[n];
    for (int i = 0; i < n; i++) {
        A[i] = new double[n+1];
    }

    // initialize the matrix with values (does not require row exchange)
    A[0][0] = 2; A[0][1] = 3; A[0][2] = -1; A[0][3] = 1;
    A[1][0] = 4; A[1][1] = 4; A[1][2] = 3; A[1][3] = 3;
    A[2][0] = 2; A[2][1] = -3; A[2][2] = 1; A[2][3] = -1;
    
    // print original matrix
    std::cout << "The original matrix (the last column is augmented) is:" << std::endl;
    printMatrix(A, n);

    // solve the system of linear equations
    solveSystem(A, n);

    // initialize the matrix with values (requires row exchange)
    A[0][0] = 0; A[0][1] = 3; A[0][2] = -1; A[0][3] = 1;
    A[1][0] = 4; A[1][1] = 4; A[1][2] = 3; A[1][3] = 3;
    A[2][0] = 2; A[2][1] = -3; A[2][2] = 1; A[2][3] = -1;
    
    // print original matrix
    std::cout << "The original matrix (the last column is augmented) is:" << std::endl;
    printMatrix(A, n);

    // solve the system of linear equations
    solveSystem(A, n);

    // free the memory used by the matrix
    for (int i = 0; i < n; i++) {
        delete[] A[i];
    }
    delete[] A;

    return 0;
}
