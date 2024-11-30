import random
import os

# ჰენგმენის კლასის განსაზღვრა (სადაც თამაშის ლოგიკას აღვწერ)
class Hangman:
    def __init__(self, word_list, max_attempts=10):
        # განვსაზღვრე დაწყებისას არჩეული სიტყვა, ცდების მაქსიმალური რაოდენობა
        self.word = random.choice(word_list).lower()  # შემთხვევითობის პრონციპით ვირჩევ სიტყვას და გადამყავს პატარა ასოებში
        self.max_attempts = max_attempts  # ცდების მოაქსიმალური ოდენობა
        self.attempts_left = max_attempts  # დარჩენილი ცდების რაოდენობა
        self.guessed_letters = []  # გამოცნობილი ასოების სია
        self.hidden_word = ["_" for _ in self.word]  # სიტყვა, რომელიც ჩავიფიქრე - ქვედა ტირეებით

    def display_word(self):
        # ამოწმებს, თუ რა ეტაპზეა ახლა სიტყვა
        return " ".join(self.hidden_word)

    def guess_letter(self, letter):
        # ამოწმებს მოთამაშის მიერ არჩეულ ასოს: ასო სწორია თუ არა, დარჩენილი ცდების რაოდენობას და ანახლებს სიტყვას
        if letter in self.guessed_letters:
            # თუ მოთამაშე მეორედ აირჩევს არჩეულ ასოს, ასე შევატყობინებ
            print("You already guessed that letter!")
            return
        self.guessed_letters.append(letter)  # გამოცნობილი ასოს დამატება, გამოცნობილი ასოების სიაში

        if letter in self.word:
            # თუ ასო სიტყვაშია, განსაზღვრავს ასოს მდებარეობას
            print(f"Good guess! {letter} is in the word.")
            for i, l in enumerate(self.word):
                if l == letter:
                    self.hidden_word[i] = letter
        else:
            # თუ მითითებული ასო არ არის სიტყვაში, შეამცირებს მცდელობებს და შეატყობინებს ამას მოთამაშეს
            self.attempts_left -= 1
            print(f"Wrong guess! {letter} is not in the word.")
            print(f"Attempts left: {self.attempts_left}")

    def is_word_guessed(self):
        # ამოწმებს დარჩა თუ არა ქვედა ტირე სიტყვაში (გამოცნობილია სრულად თუ არა)
        return "_" not in self.hidden_word

    def save_game(self, filename="hangman_save.txt"):
        # შევინახავ თამაშის მიმდინარე მდგომარეობას ფაილში, შემდგომი გაგრძელებისთვის
        with open(filename, "w") as file:
            # დავწეროთ ფაილში სიტყვა, დარჩენილი ცდების რაოდენობა, გამოცნობილი ასოები და დაფარული სიტყვის მდგომარეობა
            file.write(f"{self.word}\n")
            file.write(f"{self.attempts_left}\n")
            file.write(f"{','.join(self.guessed_letters)}\n")
            file.write(f"{','.join(self.hidden_word)}\n")
        print("Game saved!")  # შეტყობინება მოთამაშეს რომ თამაში შენახულია

    def load_game(self, filename="hangman_save.txt"):
        # შენახული თამაშის ჩატვირთვა, რათა მოთამაშემ გააგრძელოს თამაში
        if not os.path.exists(filename):
            # თუ ფაილი წაშლილია/არ არსებობს, შეატყობინებს მოთამაშეს
            print("No saved game found.")
            return
        with open(filename, "r") as file:
            # წაკითხვა და აღდგენა თამაშის ფაილის
            self.word = file.readline().strip()
            self.attempts_left = int(file.readline().strip())
            self.guessed_letters = file.readline().strip().split(",")
            self.hidden_word = file.readline().strip().split(",")
        print("Game loaded!")  # მოთამაშეს მიაწვდის ინფორმაციას რომ თამაში ჩაიტვირთა

def main():
    # მთავარი ფუნქცია , რომელიც მართავს მთელ თამაშის პროცესს
    words = ["khachapuri","lobiani","khinkali","mtsvadi","elarji"] # სიტყვების სია, საიდანაც შემთხვევითობის პრონციპით მოხდება სიტყვის არჩევა
    hangman = Hangman(words)  # ახალი ობიექტის შეცვლა

    # მისასალმებელი სიტყვა და ზოგადი ინსტრუქცია
    print("\nWelcome to Georgian Hangman!")
    print(f"You have {hangman.max_attempts} attempts to guess GEORGIAN FOOD <3")

    # მთავარი ციკლი, რომელიც თამაშოს ბოლომდე გაგრძელდება
    while hangman.attempts_left > 0 and not hangman.is_word_guessed():
        # დაუპრინტავს სიტყვის მიმდინარე მდგომარეობას, დარჩენილ ცდებს და გამოცნობილ ასოებს
        print(f"\n Word: {hangman.display_word()}\n")
        print(f"Attempts left: {hangman.attempts_left}")
        print(f"Guessed letters: {', '.join(hangman.guessed_letters)}")

        # სთხოვს მოთამაშეს ასოს გამოცნობას
        guess = input("Guess a letter: ").lower()
        if len(guess) != 1 or not guess.isalpha():
            # ვამოწმებთ რომ მოთამაშემ შეიყვანოს მხოლოდ ასო, და ისიც ერთი
            print("Invalid input! Please enter a letter (just one)")
            continue

        hangman.guess_letter(guess)  # ამუშავებს მოთამაშის მითითებულ ასოს

    # თამაშის დასასრულის ტექსტი მოთამაშისთვის მოგების ან/და წაგების შემთხვევაში
    if hangman.is_word_guessed():
        print(f"\nCongratulations! You guessed the word: {hangman.word}")
    else:
        print(f"\nGame over! The word was: {hangman.word}")


# თამაშის გაშვება (თუ სკრიპტი სწორადაა დაწერილი)
if __name__ == "__main__":
    main()
