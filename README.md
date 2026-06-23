# python-hangman-game
A simple Hangman game built with Python where players guess a hidden word one letter at a time.
"""
Hangman Game
============
A classic word-guessing game with 6 attempts allowed.
"""

import random

# ── Word list ──────────────────────────────────────────────────────────────────
WORDS = ["python", "github", "laptop", "music", "planet"]

# ── ASCII Art stages (6 wrong guesses allowed) ────────────────────────────────
HANGMAN_STAGES = [
    # 0 wrong
    """
   +---+
   |   |
       |
       |
       |
       |
=========
""",
    # 1 wrong
    """
   +---+
   |   |
   O   |
       |
       |
       |
=========
""",
    # 2 wrong
    """
   +---+
   |   |
   O   |
   |   |
       |
       |
=========
""",
    # 3 wrong
    """
   +---+
   |   |
   O   |
  /|   |
       |
       |
=========
""",
    # 4 wrong
    """
   +---+
   |   |
   O   |
  /|\\  |
       |
       |
=========
""",
    # 5 wrong
    """
   +---+
   |   |
   O   |
  /|\\  |
  /    |
       |
=========
""",
    # 6 wrong – game over
    """
   +---+
   |   |
   O   |
  /|\\  |
  / \\  |
       |
=========
""",
]

MAX_WRONG = 6


def display_state(word: str, guessed: set, wrong_count: int) -> None:
    """Print the current hangman figure and masked word."""
    print(HANGMAN_STAGES[wrong_count])
    # Show guessed letters in place, underscores for the rest
    masked = " ".join(ch if ch in guessed else "_" for ch in word)
    print(f"  Word  : {masked}")
    print(f"  Wrong guesses left : {MAX_WRONG - wrong_count}")
    if guessed:
        wrong_letters = sorted(ch for ch in guessed if ch not in word)
        print(f"  Letters tried  : {', '.join(sorted(guessed))}")
        if wrong_letters:
            print(f"  Wrong letters  : {', '.join(wrong_letters)}")
    print()


def get_guess(guessed: set) -> str:
    """Prompt the player until they enter a valid, new letter."""
    while True:
        guess = input("  Guess a letter: ").strip().lower()
        if len(guess) != 1 or not guess.isalpha():
            print("  ⚠  Please enter a single alphabet letter.\n")
        elif guess in guessed:
            print(f"  ⚠  You already tried '{guess}'. Pick another.\n")
        else:
            return guess


def play_game() -> None:
    """Run one round of Hangman."""
    word = random.choice(WORDS)
    guessed: set = set()
    wrong_count = 0

    print("\n" + "=" * 40)
    print("       🎮  HANGMAN  🎮")
    print("=" * 40)
    print(f"  The word has {len(word)} letters. Good luck!\n")

    while wrong_count < MAX_WRONG:
        display_state(word, guessed, wrong_count)

        # Win check
        if all(ch in guessed for ch in word):
            print(f"  🎉 Congratulations! You guessed it → '{word.upper()}'")
            return

        guess = get_guess(guessed)
        guessed.add(guess)

        if guess in word:
            print(f"\n  ✅  '{guess}' is in the word!\n")
        else:
            wrong_count += 1
            remaining = MAX_WRONG - wrong_count
            print(f"\n  ❌  '{guess}' is NOT in the word. {remaining} chance(s) left.\n")

    # Final state (game over)
    display_state(word, guessed, wrong_count)
    print(f"  💀 Game Over! The word was → '{word.upper()}'")


def main() -> None:
    print("\nWelcome to Hangman!")

    while True:
        play_game()
        again = input("\n  Play again? (y/n): ").strip().lower()
        if again != "y":
            print("\n  Thanks for playing! Goodbye 👋\n")
            break


if __name__ == "__main__":
    main()
