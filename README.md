import re
from collections import Counter
from spellchecker import SpellChecker
spell = SpellChecker()

# Use the spellchecker instance to perform spell checking
misspelled = spell.unknown(['apple', 'bananna', 'oraneg'])

for word in misspelled:
    # Get the most likely correction for each misspelled word
    correction = spell.correction(word)
    print(f"The corrected spelling for '{word}' is '{correction}'.")


def token_healing(input_text):
    # Preprocess the input text
    input_text = input_text.lower()  # Convert to lowercase
    input_text = re.sub(r'[^a-zA-Z0-9\s]', '', input_text)  # Remove non-alphanumeric characters

    # Tokenize the input text
    tokens = input_text.split()

    # Perform spell checking and token replacement
    spell = SpellChecker()
    healed_tokens = []
    for token in tokens:
        # Check if token is misspelled
        if not spell.unknown([token]):
            healed_tokens.append(token)
        else:
            # Get the most probable correct spelling
            corrected_token = spell.correction(token)
            healed_tokens.append(corrected_token)

    # Perform context analysis and token replacement
    context_tokens = Counter(healed_tokens)
    healed_text = []
    for token in healed_tokens:
        # Check if token appears more than once in the context
        if context_tokens[token] > 1:
            # Replace token with the most common occurrence in the context
            most_common_token = context_tokens.most_common(1)[0][0]
            healed_text.append(most_common_token)
        else:
            healed_text.append(token)

    # Reconstruct the healed text
    healed_text = ' '.join(healed_text)

    return healed_text
