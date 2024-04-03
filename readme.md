Strategy 1 used what you see in notebook.

Maximizing Single Letter Occurrence:

    Normalize the occurrence of letters in a word, considering each letter once to cover all its occurrences.
    Clean the input word by removing spaces.
    Search for similar words in a dictionary based on the cleaned input word.
    Guess the most occurring letter in the word that hasn't been guessed yet.
Vowel Ratio Analysis:

    Calculate the ratio of vowel count to the word's length.
    If the ratio exceeds 55%, deduce that it's unlikely for the word to have more vowels based on distribution patterns.
Fallback to Substring Analysis:

    If similar words are exhausted in the given dictionary, resort to breaking down the word into smaller fragments (prefixes/suffixes).
    Utilize a dictionary containing substrings of words from the original dictionary.
    Attempt to fit the most suitable letter into the word based on the analysis of these substrings.
Fallback to Common Letter Guess:
    
    If a suitable letter cannot be found in the substring dictionary, guess the most common letter across all words that hasn't been guessed yet.




**************************************************************
ANOTHER STRATEGY USING LSTM which was performing better but due to exhausted limited i couldnt check on api call, however on local it was giving accuracy of 69.98%
    How to run this program
    In command line environment, run
    
    python main.py --lives 6 --train_set "words_250000_train.txt"

    lives is the number of fail guesses assigned by you. If AI can't guess the correct word within lives fails, the AI loss.
    train_set is the path of training set. Since AI needs read english words to learn how to guess, train_set should be a .txt file contains only english words each line. Default training set contains 250000 words. Testing words may or may not appear in the training set.
Algorithm

    Guess the first letters. In this step, the algorithm will make the guess based on the letter frequency in the corresponding word group. Word group here refers to all the words of the same length in the training set. For example, if the train set is ["abc", "de", "adf"] then we have two word groups: length == 2 group and length == 3 group. In length == 2 group, "a" has the highest frequency, then our first guess will be "a" . If the letter with the highest frequency is not in the hidden word, the algorithm will continue to guess the letter with the second-highest frequency ... This step will keep going on until the algorithm has made a valid guess.
    
    Guess other letters by 2-gram. After divided all words into different groups, I calculated the frequency of the 2-grams in each group. Starting from the first letter guessed in the first step, the algorithm will calculate all the 2-grams contains the letter and chose the 2-gram with the highest frequency. This step will keep going on until the algorithm has made a valid guess. Use LSTM model to make predictions. There is also a chance that no matched 2-gram will be found. In this case, I pre-trained an LSTM model to fill in the remaining blanks. The main benefit of the model is to introduce uncertainty so that we can make predictions other than combinations we have seen in the training set. The structure of the LSTM model is as follows: input: encoded obscured word and the letters we have already guessed output: a 1 by 26 vector with each element between 0-1, indicating the probability of the next letters

Architecture
    _n is the length of word, time step is how many rounds we have guessed. The shape of X is 27 because other than 26 letters we need to preserve a space for "_". LSTM model architecture_


Training set generation
    
    I trained the model using the simulated games generated from the training set (implemented in Word2Batch() class). I generated the training set following the next procedures:
    
    randomly select a word and make is all blank
    generate encoded label of this word
    let the model makes predictions in each step and generated the whole training set.