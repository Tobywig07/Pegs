# Pegs Bot Library (C++)
This library contains all necessary components to make a Pegs bot.

# What is Pegs?
Pegs is a turn based 2 player game similar to Chess and Checkers. The goal is to get one of your Pegs to the other side of the board.
The board is a 4x6 grid and each opponent starts with 8 pegs. After white has made a move, black gets to make a move, etc.

![image](https://github.com/user-attachments/assets/e9edc669-8991-4958-a794-e2b397cdb228)

# Gameplay Rules
White always begins first.

- **Move rules**
  - A peg can move up, left and right freely, as long as there is nothing in the way, like a wall or other peg.
  - A peg can 'skip' if a peg of any color is in front of it. A skipping move is where you move up-left or up-right from the starting position.
  - A peg can capture a peg of the opponent by jumping over it diagonally. This can be in any of the four possible directions.

- **Ending the game**
  - The game has ended when a player gets a peg to the other side of the board. That player wins.
  - If a player has no pegs left, the other player automatically wins.
  - The game is a draw if the same position is repeated thrice, or if a player has no moves.

- **Move notation**
  - A written down move is similar to the chess [Algebraic notation](https://www.chess.com/terms/chess-notation).
  - The notation shows you where the piece to move is, where you want to move it and if it is a capture, or a winning move.
    - The file the peg is in, is written down as a letter (file 1 = a, file 2 = b, etc).
    - The rank the peg is in, is written down as a number (rank 1 = 1, rank 2 = 2, etc). This is written down after the file.
    - If the peg captures another peg, you add a '+' at the end.
    - If the move gets the peg to the other side of the board, you add # to the end, concluding the game.

  - The notation starts with where the peg is (example: a2), then it says where the peg is going to move (example: c4). If it is a capture, you add a '+' at the end (example: a2c4+).

# Getting Started
- Install an IDE, such as [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Install Visual C++ on your IDE.
- Download this repository and open the project.
- Open MyBot.cpp and write some code!
  - You might want to look at the Documentation below first, and the Gameplay Rules too!
  - **Bots are not supposed to use multithreading or parallelism.**
- Build and run the project to test your changes.
  - For testing, you have two options:
    - You can play the bot against yourself (HumanBot vs MyBot).
    - It can play a match against itself (MyBot vs MyBot).

# Documentation

## MyBot (class)
| Type | Definition | Notes |
| --- | --- | --- |
| Move | Think(Board board, Timer timer) | This is the function you need to implement for your bot. |


## Board (class)
| Type | Definition | Notes |
| --- | --- | --- |
| void | MakeMove(Move move) | Updates the board state with the given move. The move is assumed to be legal, and may result in errors if it is not. Note that this doesn't make the move in the actual game, it just allows you to look ahead at future possibilities. The move can be undone with the UndoMove(Move move) method.|
| void | UndoMove(Move move) | Undo a move that was made with the MakeMove method. Moves must be undone in reverse order, so for example, if moves A, B, C are made, they must be undone in the order C, B, A. |
| void | Turn() | Give the move to the other side. |
| GameResult | GetGameResult() | Returns the current result of the game. Returns `inProgress` if the game does not have a result. |
| Piece | turn | Who's turn it is to make a move on the board. |
| uint64_t | GetZobristKey() | Returns the zobrist key. This is a 64-bit hash of the current position. Learn more about this [here](https://www.chessprogramming.org/Zobrist_Hashing). |
| uint64_t\[3] | bitboards | array of 64-bit numbers where each bit set to 1 represents a square that contains a peg of the given color. 0 = white bitboard, 1 = black bitboard, 2 = all pieces bitboard. |
| vector\<uint64_t> | repetitionPositionHistory | Zobrist keys for all the positions played in the game so far. This is reset whenever a push move, skipping move or capture move is made, as previous positions are now impossible to reach again. |


## Move (struct)
| Type | Definition | Notes |
| --- | --- | --- |
| Move | Move(int startSquare, int targetSquare, int capture = 0) | Constructor for creating a move by its starting position index, target position index and, if relevant, a capture position index. |
| Move | Move(int value) | Constructor for creating a move by its move value. |
| unsigned short | moveValue | Compact (16bit) move representation to preserve memory. The format is as follows: (fffffftttttsssss). Bits 0-5: start square index. Bits 6-10: target square index. Bits 11-15: capture index |
| int | GetStartSquare() | Returns the starting square of the move. |
| int | GetTargetSquare() | Returns the target square of the move. |
| int | GetCapture() | Returns the capture square of the move. |
| int | GetDirection() | Returns the offset of the starting square and the target square. |
| bool | IsEmpty() | Returns true if the moveValue is 0, otherwise false. |


## Piece (enum)
| Type | Definition | Notes |
| --- | --- | --- |
| Piece | black | Value of -1. Represents a black piece. |
| none | black | Value of 0. Represents no piece. |
| white | black | Value of 1. Represents a white piece. |


## GameResult (enum)
| Type | Definition | Notes |
| --- | --- | --- |
| GameResult | inProgress | The game is still going. |
| GameResult | inconclusive | The game is inconclusive. |
| GameResult | draw | The game is a draw. Neither side wins. |
| GameResult | whiteWin | White wins. |
| GameResult | blackWin | Black wins. |


## Timer (class)
| Type | Definition | Notes |
| --- | --- | --- |
| long long | GetGameStartTimeMiliseconds() | The amount of time (in milliseconds) that each player started the game with. |
| long long | GetMilisecondsElapsedThisTurn() | The amount of time elapsed since the current player started thinking (in milliseconds). |
| long long | GetMilisecondsRemaining() | The amount of time left on the clock for the current player (in milliseconds). |
| long long | GetOpponentMilisecondsRemaining() | The amount of time left on the clock for the other player (in milliseconds). |


## BoardHelper
| Type | Definition | Notes |
| --- | --- | --- |
| const int | Infinity | ~32-bit integer limit: 2147000000. |
| constexpr uint64_t | FileA | Bits of the first file. |
| constexpr uint64_t | FileB | Bits of the second file. |
| constexpr uint64_t | FileC | Bits of the third file. |
| constexpr uint64_t | FileD | Bits of the fourth file. |
| constexpr uint64_t | Rank1 | Bits of the first rank. |
| constexpr uint64_t | Rank2 | Bits of the second rank. |
| constexpr uint64_t | Rank3 | Bits of the third rank. |
| constexpr uint64_t | Rank4 | Bits of the fourth rank. |
| constexpr uint64_t | Rank5 | Bits of the fifth rank. |
| constexpr uint64_t | Rank6 | Bits of the sixth rank. |
| constexpr uint64_t | notAFile | All bits, except the A file. |
| constexpr uint64_t | notDFile | All bits, except the D file. |
| constexpr uint64_t | innerFileMask | All bits, except the A and D file. |
| constexpr uint64_t | outerFileMask | All bits, except the B and C file. |
| constexpr uint64_t | LightSquares | Bits of the white squares. |
| constexpr uint64_t | DarkSquares | Bits of the black squares. |
| int | PopLSB(uint64_t& value) | Returns index of the first bit that is set to 1. The bit will also be cleared to zero. This can be useful for iterating over all the set squares in a bitboard. |
| int | GetLSB(uint64_t& value) | Returns index of the first bit that is set to 1. |
| int | CountBits(uint64_t value) | Returns the number of bits that set to 1 in the given bitboard. |
| void | SetSquare(uint64_t& bitboard, int squareIndex) | Set the given square on the bitboard to 1. |
| void | ClearSquare(uint64_t& bitboard, int squareIndex) | Clear the given square on the bitboard to 0. |
| void | ToggleSquares(uint64_t& bitboard, int squareA, int squareB) | Toggle the given two squares on the bitboard between 0 and 1. This can be useful when making a move. |
| bool | SquareIsSet(uint64_t bitboard, int squareIndex) | Returns true if the given square is set to 1 on the bitboard, otherwise false. |
| bool | ValidSquare(int x, int y) | Returns true if given x and y is not out of bounds, otherwise false. |
| bool | ValidSquare(int x, int y, int& index) | Returns true if given x and y is not out of bounds, otherwise false. Also outputs the index of the square on x and y |
| int | GetSquareIndex(int x, int y) | Returns the square index of file and rank. |
| int | FileIndex(int squareIndex) | Returns the file of the given square index. Starts at 1. |
| int | RankIndex(int squareIndex) | Returns the rank of the given square index. Starts at 1. |

