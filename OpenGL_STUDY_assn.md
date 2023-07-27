# 0726 Assignment
## 1번째 문제: 타블렛 모드
> 구현하고자 하는 목표 이미지   
> <img src="/images/opengl_STUDY_assn_0726(1).jpeg" width="40%" height="40%"></img>

```C++
#include <OpenGL/OpenGL.h>
#include <GLUT/GLUT.h>
#include <iostream>
#include <vector>
using namespace std;

GLint ClickX = 0;
GLint ClickY = 0;
vector<pair<int, int>> p;

void Draw() {
    glClearColor(1.0, 1.0, 1.0, 1.0);
    glClear(GL_COLOR_BUFFER_BIT);
    glColor3f(0.0, 0.0, 1.0);
    glPointSize(10);
    glBegin(GL_POINTS);
    for(auto & point : p) glVertex2f(point.first / 500.0, (500 - point.second) / 500.0);
    glEnd();
    glFlush();
    }

void MouseMove(GLint X, GLint Y) {
    ClickX = X;
    ClickY = Y;
    p.push_back(make_pair(ClickX, ClickY));
    glutPostRedisplay();
}

int main(int argc, char *argv[]) {
    glutInit(&argc, argv);
    glutInitWindowSize(500, 500);
    glutCreateWindow("Tablet Mode");
    
    glOrtho(0.0, 1.0, 0.0, 1.0, -1.0, 1.0);
    glutDisplayFunc(Draw);
    glutMotionFunc(MouseMove);
    
    glutMainLoop();
    return 0;
}
```
### 사용된 주요 문법
**MouseFunc - MotionFunc(마우스 이동시 발생하는 이벤트 함수)**
**vector**
**GL_POINTS**
   
### 구현 방법
* 두꺼운 선을 표현해야 하기 때문에 도형을 GL_POINTS로 설정하고 포인트 두께를 10으로 두껍게 주었다.
* 마우스를 드래그 하며 학번과 이름을 쓰는 것이 조건이기 때문에, MotionFunc 함수를 이용했다.
* **glClear()를 사용하되, 전에 그렸던 그림을 그대로 유지하기 위해서** vector를 이용해서 배열에 x, y 좌표를 저장하고 그것을 for-each문을 통해 계속 덮어씌우는 식으로 진행했다.

## 구현 결과

***

## 2번째 문제: 화면 채우기
> 구현 목표:
> -1 <= x, y <= 1인 공간을 10x10으로 균등 분할한 후, 타이머를 활용하여 각각의 그리드를 좌하단 -> 좌상단 -> ... -> 우하단 -> 우상단 순으로 랜덤 색 채우기

```C++
#include <GLUT/GLUT.h>
#include <OpenGL/OpenGL.h>
#include <stdlib.h>
#include <iostream>
#include <vector>
using namespace std;

int grid = 10;
int currX = 0;
int currY = 0;

struct ColoredSquare {
    int x;
    int y;
    double r;
    double g;
    double b;
};

vector<ColoredSquare> s;

void Square() {
    glClear(GL_COLOR_BUFFER_BIT);
    glBegin(GL_QUADS);
    for (const auto & square : s) {
        glColor3f(square.r, square.g, square.b);
        glVertex2f(-1.0 + square.x * 0.2, -1.0 + square.y * 0.2);
        glVertex2f(-1.0 + square.x * 0.2, -0.8 + square.y * 0.2);
        glVertex2f(-0.8 + square.x * 0.2, -0.8 + square.y * 0.2);
        glVertex2f(-0.8 + square.x * 0.2, -1.0 + square.y * 0.2);
    }
    glEnd();
    glFlush();
}

void MoveSquare(int value) {
    glutPostRedisplay();

    double r = (double)(rand() % 100) / 100;
    double g = (double)(rand() % 100) / 100;
    double b = (double)(rand() % 100) / 100;

    s.push_back({currX, currY, r, g, b});

    currY++;
    if (currY >= grid) {
        currY = 0;
        currX++;
    }

    glutTimerFunc(100, MoveSquare, 1);
}

int main(int argc, char *argv[]) {
    glutInit(&argc, argv);
    glutCreateWindow("Random Color Square Grid");

    glutDisplayFunc(Square);
    glutTimerFunc(100, MoveSquare, 1);
    glutMainLoop();
    return 0;
}
```
