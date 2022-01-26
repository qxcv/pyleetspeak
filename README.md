# Pyleetspeak :one::three::three::seven::robot:


Currently in developing process. Stay tuned for new releases related to new word camouflaging methods and NER data generation.


  - [Overview](#overview)
  - [Installation](#installation)
  - [**Word camouflaging**](#word-camouflaging)
    - [**LeetSpeaker**](#leetspeaker)
      - [Parameters](#parameters)
      - [Modes](#modes)
      - [Basic Use](#basic-use)
      - [Define your own changes](#define-your-own-changes)
      - [Uniform substitutions](#uniform-substitutions)
      - [Get all changes](#get-all-changes)


## Overview

---

Word camouflage is currently used to evade content moderation in Social Media. Therefore, this tool aims to counter new misinformation that emerges in social media platforms by providing a mechanism for simulating and generating leetspeak/word camouflaging data. 


`pyleetspeak` includes three different, but compatible, text modifications word camouflaging methods: `LeetSpeaker`, `PuntctuationCamouflage` and `InversionCamouflage`.

- `LeetSpeaker`: This module apply the canonical 'leetspeak' method of producing visually similar character strings by replacing alphabet characters with special symbols or numbers. There's many different ways you can use leet speak. Ranging from basic vowel substitutions to really advanced combinations of various punctuation marks and glyphs. Different leetspeak levels are included.
- `PuntctuationCamouflage`: This module apply punctuation symbol injections in the text. It is another version of producing visually similar character strings. The location of the punctuation injections and the symbols used can be selected by the user. 
- `InversionCamouflage`: This module create new camouflaged version of words by inverting the order of the syllables. It works by separating a input text in syllabels, select two syllabels and invert them.

These modules can be combined into a string to generate a leetspeak version of an input text. Precisely, this can be achieved by using the `Leet_NER_generator` method that selects the most semantically relevant words from an input text, applies word camouflage and creates compatible annotations for NER detection.

UML diagrams

![edited_classes_package-0](https://user-images.githubusercontent.com/56938752/151244448-9fb8b7b8-103f-42f6-8435-cd1f6edfb88b.png)

<<<<<<< Updated upstream

![packages_Pyleetspeak-0](https://user-images.githubusercontent.com/56938752/151245864-15b97644-9f37-4a48-8454-ae8824021014.png)

=======
![packages_Pyleetspeak-0](https://user-images.githubusercontent.com/56938752/151245864-15b97644-9f37-4a48-8454-ae8824021014.png)


>>>>>>> Stashed changes
## Installation

---

````bash
pip install pyleetspeak
````


## **Word camouflaging**

---

### **LeetSpeaker**

Canonical [leetspeak](https://en.wikipedia.org/wiki/Leet) in which standard letters are often replaced by numerals or special characters that resemble the letters in appearance

---

#### **Parameters**


You can see an example of use in a Heroku App:

<https://user-images.githubusercontent.com/56938752/147962824-c347e184-14b6-41fe-8b05-ef670ac0a5f9.mp4>

The only required argument that the user has to provide is the `text_in` argument which represent the casual text to transform to leetspeak. Nonetheless, there are other optional arguments that control the behaviour of the transformation:`

- `change_prb` determines the probability of a transformation to take place (i.e, if it is equal 1 all the possible transformation will be applied).
- `change_frq` is affects how frequently a transformation will occur (i.e, if it is equal 1 all the letters of this transformation type will be changed).
- `mode` controls the level of leetspeak transformation. Currently only `basic` mode is available. We are working on more modes. Stay tuned.
- `seed` controls the reproducibility of the results. By default no seed is applied.
- `verbose` controls the verbosity of the proccess.
- `get_all_combs` to obtain all the possible leetspeak versions of a casual text
- `uniform_change` determines if the same substitution character should be used in all the positions where the casual text will be modified.

Minor concerns about the package behaviour: accents are deleted using `Unidecode`. This is important for languages like Spanish, where the word "melocotón" is preprocessed as "melocoton" and finally transformed to leetspeak.

---


#### **Modes**


There are several modes available:

- `basic`
- `intermediate`
- `advanced`
- `covid_basic`
- `covid_intermediate`

---

#### **Basic Use**

Let's see a simple working example:

````python
from pyleetspeak import LeetSpeaker

text_in = "I speak leetspeak"
leeter = LeetSpeaker(
    change_prb=0.8, change_frq=0.6, mode="basic", seed=None, verbose=False
)
leet_result = leeter.text2leet(text_in)
print(leet_result)
````

For the sake of reproducibility you can set a random seed:

````python
from pyleetspeak import LeetSpeaker

leeter = LeetSpeaker(
    change_prb=0.8,
    change_frq=0.5,
    mode="basic",
    seed=42,  # for reproducibility purposes
    verbose=False,
)
leet_result = leeter.text2leet(text_in)
print(leet_result)
# "1 sp34k leetsp3ak"
````
---

#### **Define your own changes**

`pyleetspeak` is prepared to apply substitutions defined by the user. It is essential to highlight that these new user-defined changes have to follow two possible formats, dictionary or List of tuples. Here we show a toy example to add two new target characters from the original text to be replaced by two and one different characters, respectively:

- Dictionary type:

  ````python
  {"target_chr_1": ["sub_chr_1", "sub_chr_1"], "target_chr_2": ["sub_chr_1"]}
  ````

- List[Tuple] type:
  
  ````python
  [("target_chr_1", ["sub_chr_1", "sub_chr_1"]), (("target_chr_2", ["sub_chr_1"])]
  ````

You can add new user-defined substitutions:

````python
from pyleetspeak import LeetSpeaker

text_in = "New changes Leetspeak"
letter = LeetSpeaker(
    change_prb=1,
    change_frq=0.8,
    mode="basic",
    seed=21,
    verbose=False,
    get_all_combs=False,
    user_changes=[("a", "#"), ("s", "$")],  # user-defined changes
)
print(letter.text2leet(text_in))
# N3w ch@ng3$ L33t$pe4k
````

Moreover, you can use only the user-defined substitutions:

````python
from pyleetspeak import LeetSpeaker

text_in = "Only user changes: Leetspeak"
letter = LeetSpeaker(
    change_prb=1,
    change_frq=0.8,
    mode=None, # None pre-defined changes will be applied
    seed=21,
    verbose=False,
    get_all_combs=False,
    user_changes = [("a", "#"), ("s", "$")], # user-defined changes
)
print(letter.text2leet(text_in))
# Only u$er ch#nge$: Leet$pe#k
````
---

#### **Uniform substitutions**

Usually, the same substitution character is used in all the matches for a specific substitution type. In other words, the same target character is usually replaced by the same substitution character. In order to reproduce this situation, `pyleetspeak` includes the `uniform_change` parameter that determines if all the matches of a target character are jointly or independently substituted. In the following example notice how the target character "e" is always replaced by "€" when `uniform_changes` is se to `True`.

````python
from pyleetspeak import LeetSpeaker

text_in = "Leetspeak"
leeter = LeetSpeaker(
    change_prb=1,  # All subs type will occur
    change_frq=1,  # All matches of target chr will be changed
    mode="basic",
    seed=41,
    user_changes=[
        ("e", ["3", "%", "€", "£"])
    ],  # Add diferent subs characters for target chr "e"
    uniform_change=True,  # Use the same substitution chr for each target chr
)
print(leeter.text2leet(text_in))
# L€€tsp€4k
````

---

#### **Get all changes**

You can also obtain all the possible versions of a leetspeak text using the `get_all_combs` parameter like this:

````python
from pyleetspeak import LeetSpeaker

text_in = "leetspeak"
leeter = LeetSpeaker(
    mode="basic",
    get_all_combs=True,
    user_changes = [("e", "€"), ("s", "$")], # user-defined changes
)
leet_result = leeter.text2leet(text_in)
print(len(leet_result))
assert len(leet_result) == 162 # all possible combinations
leet_result[20]
# 162
# 'le3t$p34k'
````

If you are only interested in the combinations that apply the same substitution character for each target target, you can also set `uniform_change` to `True`.

````python
from pyleetspeak import LeetSpeaker

text_in = "leetspeak"
leeter = LeetSpeaker(
    mode="basic",
    get_all_combs=True,
    user_changes = [("e", "€"), ("s", "$")], # user-defined changes
    uniform_change = True
)
leet_result = leeter.text2leet(text_in)
print(len(leet_result))
assert len(leet_result) == 90 # all possible combinations
leet_result[60]
# 90
# 'le3t$peak'
````

---
