#include <iostream>
#include <string>
#include <cstdlib>
#include <ctime>
#include <fstream>
#include <unistd.h>
#include <iomanip>
using namespace std;

#define RESET   "\033[0m"
#define RED     "\033[31m"
#define GREEN   "\033[32m"
#define CYAN    "\033[36m"
#define YELLOW  "\033[33m"
#define MAGENTA "\033[35m"
#define BLUE    "\033[34m"

// Clear screen function
void clearScreen() {
#ifdef _WIN32
    system("cls");
#else
    system("clear");
#endif
}

class Player {
private:
    string name;
    int position;
public:
    Player(string playerName) : name(playerName), position(1) {}
    string getName() { return name; }
    int getPosition() { return position; }
    void setPosition(int pos) { position = pos; }
};

class Dice {
public:
    Dice() { srand(time(0)); }
    int roll() {
        cout << "Rolling dice";
        for (int i = 0; i < 3; ++i) {
            cout << ".";
            cout.flush();
            sleep(1);
        }
        int result = (rand() % 6) + 1;
        cout << "\n🎲 Dice rolled: " << YELLOW << result << RESET << endl;
        return result;
    }
};

class Board {
private:
    int board[101];
    bool isSnake[101];
    bool isLadder[101];
public:
    Board() {
        for (int i = 0; i <= 100; ++i) {
            board[i] = 0;
            isSnake[i] = false;
            isLadder[i] = false;
        }

        board[99] = 54; isSnake[99] = true;
        board[90] = 48; isSnake[90] = true;
        board[77] = 25; isSnake[77] = true;
        board[60] = 41; isSnake[60] = true;
        board[45] = 5;  isSnake[45] = true;

        board[4] = 25;  isLadder[4] = true;
        board[12] = 45; isLadder[12] = true;
        board[22] = 58; isLadder[22] = true;
        board[33] = 69; isLadder[33] = true;
        board[50] = 92; isLadder[50] = true;
    }

    int checkSnakesAndLadders(int pos) {
        if (board[pos] == 0)
            return pos;
        else if (board[pos] < pos) {
            cout << RED << "😱 Snake! Down from " << pos << " to " << board[pos] << RESET << endl;
            return board[pos];
        } else {
            cout << GREEN << "🎉 Ladder! Up from " << pos << " to " << board[pos] << RESET << endl;
            return board[pos];
        }
    }

    void showCell(int number, int pos1, int pos2) {
        if (number == pos1 && number == pos2)
            cout << "[" << YELLOW << "P1&P2" << RESET << "]";
        else if (number == pos1)
            cout << "[" << GREEN << " P1 " << RESET << "]";
        else if (number == pos2)
            cout << "[" << MAGENTA << " P2 " << RESET << "]";
        else if (isSnake[number])
            cout << "[" << RED << " S  " << RESET << "]";
        else if (isLadder[number])
            cout << "[" << BLUE << " L  " << RESET << "]";
        else
            cout << "[" << setw(3) << number << " ]";
    }
};

class Game {
private:
    Player player1, player2;
    Dice dice;
    Board board;
    bool isVsAI;

public:
    Game(string name1, string name2, bool aiMode = false)
        : player1(name1), player2(name2), isVsAI(aiMode) {}

    void displayBoard() {
        cout << "\n📦 " << CYAN << "Game Board (S=Snake, L=Ladder):" << RESET << "\n";
        for (int row = 9; row >= 0; row--) {
            for (int col = 0; col < 10; col++) {
                int number = (row % 2 == 0) ? (row * 10 + col + 1) : (row * 10 + (9 - col) + 1);
                board.showCell(number, player1.getPosition(), player2.getPosition());
            }
            cout << "\n";
        }
    }

    void start() {
        bool gameOver = false;
        Player* currentPlayer = &player1;

        while (!gameOver) {
            clearScreen(); // clear screen before new move
            displayBoard();

            cout << "\n👉 " << CYAN << currentPlayer->getName() << RESET << "'s turn. ";

            if (isVsAI && currentPlayer->getName() == "Computer") {
                cout << "(AI rolling...)\n";
                sleep(1);
            } else {
                cout << "Press Enter to roll the dice...";
                cin.ignore();
            }

            int roll = dice.roll();

            int newPos = currentPlayer->getPosition() + roll;

            if (newPos > 100) {
                cout << "⚠️ Can't move. Need exact number.\n";
            } else {
                newPos = board.checkSnakesAndLadders(newPos);
                currentPlayer->setPosition(newPos);
                cout << currentPlayer->getName() << " moved to " << newPos << endl;
            }

            sleep(2);

            if (newPos == 100) {
                clearScreen();
                displayBoard();
                cout << "\n🏆 " << GREEN << currentPlayer->getName() << RESET << " wins the game!\n";

                ofstream file("winners.txt", ios::app);
                if (file.is_open()) {
                    time_t now = time(0);
                    char* dt = ctime(&now);
                    file << currentPlayer->getName() << " won on " << dt << "\n";
                    file.close();
                    cout << "📁 Winner saved to winners.txt\n";
                }

                gameOver = true;
            }

            if (!gameOver)
                currentPlayer = (currentPlayer == &player1) ? &player2 : &player1;

            if (isVsAI && currentPlayer->getName() == "Computer" && !gameOver)
                sleep(1);
        }
    }
};

int main() {
    char playAgain;

    do {
        ifstream infile("winners.txt");
        cout << "\n🏆 " << YELLOW << "Previous Winners:" << RESET << "\n";
        if (infile.is_open()) {
            string line;
            bool empty = true;
            while (getline(infile, line)) {
                if (!line.empty()) {
                    cout << "  - " << line << endl;
                    empty = false;
                }
            }
            infile.close();
            if (empty) cout << "  (No winners yet)\n";
        } else {
            cout << "  (No winner file found yet)\n";
        }

        int mode;
        cout << "\n🎮 " << CYAN << "Welcome to Snakes and Ladders!" << RESET << "\n";
        while (true) {
            cout << "Choose mode:\n";
            cout << "1. Single Player (vs Computer)\n";
            cout << "2. Two Players\n";
            cout << "Enter option (1 or 2): ";
            cin >> mode;

            if (cin.fail() || (mode != 1 && mode != 2)) {
                cin.clear();
                cin.ignore(1000, '\n');
                cout << RED << "❌ Invalid input! Please enter 1 or 2 only.\n\n" << RESET;
            } else {
                break;
            }
        }

        string name1, name2;

        if (mode == 1) {
            cout << "Enter your name: ";
            cin >> name1;
            name2 = "Computer";
        } else {
            cout << "Enter Player 1 name: ";
            cin >> name1;
            cout << "Enter Player 2 name: ";
            cin >> name2;
        }

        Game game(name1, name2, mode == 1);
        cin.ignore();
        game.start();

        cout << "\n🔁 Do you want to play again? (y/n): ";
        cin >> playAgain;
        cin.ignore();

    } while (playAgain == 'y' || playAgain == 'Y');

    cout << "\n👋 " << MAGENTA << "Thanks for playing! Goodbye." << RESET << "\n";
    return 0;
}

