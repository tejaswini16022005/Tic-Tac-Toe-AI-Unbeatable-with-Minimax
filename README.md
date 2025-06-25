import math

# Initialize the board
board = [' ' for _ in range(9)]  # 3x3 board as a flat list

# Display the board
def print_board():
    for row in [board[i*3:(i+1)*3] for i in range(3)]:
        print('| ' + ' | '.join(row) + ' |')

# Check for a win
def check_winner(player):
    win_conditions = [
        [0, 1, 2], [3, 4, 5], [6, 7, 8],  # rows
        [0, 3, 6], [1, 4, 7], [2, 5, 8],  # columns
        [0, 4, 8], [2, 4, 6]              # diagonals
    ]
    return any(all(board[i] == player for i in condition) for condition in win_conditions)

# Check if board is full
def is_full():
    return ' ' not in board

# Get available moves
def available_moves():
    return [i for i, spot in enumerate(board) if spot == ' ']

# Minimax algorithm with alpha-beta pruning
def minimax(is_maximizing, alpha, beta):
    if check_winner('O'):
        return {'score': 1}
    elif check_winner('X'):
        return {'score': -1}
    elif is_full():
        return {'score': 0}

    if is_maximizing:
        best = {'score': -math.inf}
        for move in available_moves():
            board[move] = 'O'
            sim_score = minimax(False, alpha, beta)
            board[move] = ' '
            sim_score['move'] = move

            if sim_score['score'] > best['score']:
                best = sim_score

            alpha = max(alpha, sim_score['score'])
            if beta <= alpha:
                break
        return best
    else:
        best = {'score': math.inf}
        for move in available_moves():
            board[move] = 'X'
            sim_score = minimax(True, alpha, beta)
            board[move] = ' '
            sim_score['move'] = move

            if sim_score['score'] < best['score']:
                best = sim_score

            beta = min(beta, sim_score['score'])
            if beta <= alpha:
                break
        return best

# Human move
def human_turn():
    while True:
        try:
            move = int(input("Enter your move (1-9): ")) - 1
            if board[move] == ' ':
                board[move] = 'X'
                break
            else:
                print("Spot already taken!")
        except (ValueError, IndexError):
            print("Invalid input. Please choose from 1 to 9.")

# AI move
def ai_turn():
    print("AI's Turn:")
    move = minimax(True, -math.inf, math.inf)['move']
    board[move] = 'O'

# Game loop
def play_game():
    print("Welcome to Tic-Tac-Toe!")
    print_board()

    while True:
        human_turn()
        print_board()
        if check_winner('X'):
            print("You win!")
            break
        if is_full():
            print("It's a tie!")
            break

        ai_turn()
        print_board()
        if check_winner('O'):
            print("AI wins!")
            break
        if is_full():
            print("It's a tie!")
            break

play_game()
