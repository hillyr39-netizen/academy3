
import sys


def main():
    try:
        n = int(sys.argv[1])

        if n <= 0:
            print("N must be positive")
            return

        file = open("alice.txt", "r")
        text = file.read().lower()
        file.close()

        words = text.split()
        wordCount = {}

        for word in words:
            word = word.replace(".", "")
            word = word.replace(",", "")
            word = word.replace("!", "")
            word = word.replace("?", "")
            word = word.replace(":", "")
            word = word.replace(";", "")
            word = word.replace('"', "")
            word = word.replace("'", "")
            word = word.replace("(", "")
            word = word.replace(")", "")

            if word in wordCount:
                wordCount[word] += 1
            else:
                wordCount[word] = 1

        sortedWords = sorted(wordCount, key=wordCount.get, reverse=True)

        for i in range(min(n, len(sortedWords))):
            word = sortedWords[i]
            print(i + 1, '- word "' + word + '"', wordCount[word], "times")

    except IndexError:
        print("Please enter N")

    except ValueError:
        print("N must be a number")

    except FileNotFoundError:
        print("File not found")


if __name__ == "__main__":
    main()
