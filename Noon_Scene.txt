#include <GL/glut.h>
#include <cmath>
#include <cstdlib>
#include <vector>

struct Hut {
    float x;
    float y;
    float width;
    float height;

    Hut(float x, float y, float width, float height)
        : x(x), y(y), width(width), height(height) {}
};

std::vector<Hut> huts;

bool isOverlap(float x, float y, float width, float height) {
    for (size_t i = 0; i < huts.size(); ++i) {
        const Hut& hut = huts[i];
        if (x < hut.x + hut.width + 5 &&
            x + width + 5 > hut.x &&
            y < hut.y + hut.height + 5 &&
            y + height + 5 > hut.y) {
            return true;
        }
    }
    return false;
}

bool isNearRiver(float x, float y, float width, float height) {
    float riverX1 = -100;
    float riverX2 = 100;
    float riverY1 = -500;
    float riverY2 = 150;

    return x < riverX2 && x + width > riverX1 && y < riverY2 && y + height > riverY1;
}

void drawMountains() {
    // Darken the mountains
    glColor3f(0.5f, 0.25f, 0.0f); // Dark brown color for mountains

    // Left-side mountains
    // Mountain 1
    glBegin(GL_TRIANGLES);
    glVertex2f(-600, 150);
    glVertex2f(-500, 350);
    glVertex2f(-400, 150);
    glEnd();

    // Mountain 2
    glBegin(GL_TRIANGLES);
    glVertex2f(-400, 150);
    glVertex2f(-300, 350);
    glVertex2f(-200, 150);
    glEnd();

    // Right-side mountains
    // Mountain 3
    glBegin(GL_TRIANGLES);
    glVertex2f(-200, 150);
    glVertex2f(-100, 350);
    glVertex2f(0, 150);
    glEnd();

    // Mountain 4
    glBegin(GL_TRIANGLES);
    glVertex2f(0, 150);
    glVertex2f(100, 350);
    glVertex2f(200, 150);
    glEnd();

    // Additional mountains
    // Mountain 5
    glBegin(GL_TRIANGLES);
    glVertex2f(200, 150);
    glVertex2f(300, 350);
    glVertex2f(400, 150);
    glEnd();

    // Mountain 6
    glBegin(GL_TRIANGLES);
    glVertex2f(400, 150);
    glVertex2f(500, 350);
    glVertex2f(600, 150);
    glEnd();
}

void drawRiver() {
    // Darken the river
    glColor3f(0.0f, 0.0f, 0.4f); // Dark blue color for the river
    glBegin(GL_POLYGON);
    glVertex2f(-100, -500);
    glVertex2f(0, 150);
    glVertex2f(100, -500);
    glEnd();
}

void drawHuts() {
    for (size_t i = 0; i < huts.size(); ++i) {
        const Hut& hut = huts[i];
        float hutX = hut.x;
        float hutY = hut.y - 150.0f; // Move all huts more lower

        // Darken the huts
        glColor3f(0.3f, 0.2f, 0.1f); // Dark brown color for huts

        // Main body of the hut
        glBegin(GL_QUADS);
        glVertex2f(hutX, hutY);
        glVertex2f(hutX, hutY + hut.height);
        glVertex2f(hutX + hut.width, hutY + hut.height);
        glVertex2f(hutX + hut.width, hutY);
        glEnd();

        // Darken the door
        glColor3f(0.2f, 0.1f, 0.0f); // Dark brown color for doors

        // Door
        glBegin(GL_QUADS);
        glVertex2f(hutX + 7, hutY);
        glVertex2f(hutX + 7, hutY + 15);
        glVertex2f(hutX + 13, hutY + 15);
        glVertex2f(hutX + 13, hutY);
        glEnd();

        // Darken the roof
        glColor3f(0.25f, 0.15f, 0.05f); // Dark brown color for the roof

        // Roof of the hut
        glBegin(GL_TRIANGLES);
        glVertex2f(hutX - 3, hutY + hut.height);
        glVertex2f(hutX + hut.width / 2, hutY + hut.height + 10);
        glVertex2f(hutX + hut.width + 3, hutY + hut.height);
        glEnd();
    }
}

void drawDarkYellowSun() {
    // Dark yellow color for the sun
    glColor3f(0.8f, 0.8f, 0.2f);
    glBegin(GL_POLYGON);
    for (int i = 0; i < 360; i += 5) {
        float theta = i * 3.14159 / 180;
        float x = 50 * cos(theta);
        float y = 50 * sin(theta) + 380;
        glVertex2f(x, y);
    }
    glEnd();
}

void drawAfternoonScenery() {
    glClearColor(0.4f, 0.6f, 1.0f, 1.0f); // Light blue for the sky
    glClear(GL_COLOR_BUFFER_BIT);

    // Darker green grass below the mountains during the afternoon
    glColor3f(0.2f, 0.4f, 0.2f); // Dark green color for grass during the afternoon
    glBegin(GL_POLYGON);
    glVertex2f(-600, -500);
    glVertex2f(-600, 150);
    glVertex2f(600, 150);
    glVertex2f(600, -500);
    glEnd();

    drawDarkYellowSun();
    drawMountains();
    drawRiver();
    drawHuts();

    glFlush();
}

void display() {
    drawAfternoonScenery();
    glutSwapBuffers();
}

int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutCreateWindow("Dark Afternoon Village Scenery");
    glutInitWindowSize(800, 600);
    glutDisplayFunc(display);

    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(-600, 600, -500, 500);

    // Place 40 huts away from each other
    for (int i = 0; i < 55; i++) {
        float hutX, hutY, hutWidth, hutHeight;
        do {
            hutX = -580 + rand() % 1200;  // Increased scattering range
            hutY = -250 + rand() % 400;   // Adjusted Y-coordinate range
            hutWidth = 20.0f;
            hutHeight = 30.0f;
        } while (isOverlap(hutX, hutY, hutWidth, hutHeight) || isNearRiver(hutX, hutY, hutWidth, hutHeight));

        huts.push_back(Hut(hutX, hutY, hutWidth, hutHeight));
    }

    glutMainLoop();
    return 0;
}
