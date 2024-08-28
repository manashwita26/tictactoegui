# tictactoegui
import tkinter as tk
from tkinter import messagebox

window = tk.Tk()
window.title("Tic-Tac-Toe")
window.geometry("300x350")

present_player = "X"
but = [[None, None, None], [None, None, None], [None, None, None]]

game_over = False

def check_winner():
    """Check the game board to see if there's a winner."""
    global game_over

    for i in range(3):
        if but[i][0]["text"] == but[i][1]["text"] == but[i][2]["text"] != "":
            game_over = True
            return but[i][0]["text"]

        if but[0][i]["text"] == but[1][i]["text"] == but[2][i]["text"] != "":
            game_over = True
            return but[0][i]["text"]

    if but[0][0]["text"] == but[1][1]["text"] == but[2][2]["text"] != "":
        game_over = True
        return but[0][0]["text"]

    if but[0][2]["text"] == but[1][1]["text"] == but[2][0]["text"] != "":
        game_over = True
        return but[0][2]["text"]

    return None

def check_draw():
    """Check if the game is a draw."""
    for row in but:
        for but in row:
            if but["text"] == "":
                return False
    return True

def minimax(board, depth, is_maximizing):
    """Minimax algorithm to determine the best move for comp."""
    winner = check_winner()
    if winner == "X":
        return -1
    elif winner == "O":
        return 1
    elif check_draw():
        return 0

    if is_maximizing:
        best_score = -float('inf')
        for i in range(3):
            for j in range(3):
                if board[i][j]["text"] == "":
                    board[i][j]["text"] = "O"
                    score = minimax(board, depth + 1, False)
                    board[i][j]["text"] = ""
                    best_score = max(score, best_score)
        return best_score
    else:
        best_score = float('inf')
        for i in range(3):
            for j in range(3):
                if board[i][j]["text"] == "":
                    board[i][j]["text"] = "X"
                    score = minimax(board, depth + 1, True)
                    board[i][j]["text"] = ""
                    best_score = min(score, best_score)
        return best_score

def best_move():
    """Determine the best move for comp."""
    best_score = -float('inf')
    move = None
    for i in range(3):
        for j in range(3):
            if but[i][j]["text"] == "":
                but[i][j]["text"] = "O"
                score = minimax(buttons, 0, False)
                but[i][j]["text"] = ""
                if score > best_score:
                    best_score = score
                    move = (i, j)
    return move

def but_click(row, col):
    """Handle button click for player move."""
    global present_player

    if but[row][col]["text"] == "" and not game_over:
        but[row][col]["text"] = present_player
        winner = check_winner()
        if winner:
            messagebox.showinfo("Tic-Tac-Toe", f"{winner} wins!")
            return
        elif check_draw():
            messagebox.showinfo("Tic-Tac-Toe", "It's a draw!")
            return

        if present_player == "X":
            present_player = "O"
            move = best_move()
            if move:
                but_click(move[0], move[1])
            present_player = "X"
        else:
            present_player = "X"

def restart_game():
    """Restart the game."""
    global present_player, game_over
    present_player = "X"
    game_over = False
    for i in range(3):
        for j in range(3):
            but[i][j]["text"] = ""

for i in range(3):
    for j in range(3):
        but[i][j] = tk.Button(window, text="", font=('normal', 40), width=5, height=2,
                                  command=lambda i=i, j=j: but_click(i, j))
        but[i][j].grid(row=i, column=j)

restart_but = tk.Button(window, text="Restart", font=('normal', 20), command=restart_game)
restart_but.grid(row=3, column=0, columnspan=3)

window.mainloop()

