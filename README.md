# WordGuesser-
A word-guessing game where the player enters characters or words, attempting to discover a hidden 5-letter noun

//Purpose: Provide entertainment, as well as improve logical thinking and vocabulary skills. 
var totalLives = 10;
var textBoxesshown;
var newGuesses = "Previous Guesses will show up here!";
var displayedIDSSum = 0;
var incremented = false;

//Sets the text of the life and guess box
setText("previousGuessBox", newGuesses);
setText("livesBox", "Lives Left:" + totalLives);

//Changes the screen to the guessing screen when the start button is clicked
onEvent("startButton", "click", function( ) {
  setScreen("guessingGamescreen");
});

//Resets all aspects of the code including life count, guess box,the screen, 
//and chooses a new word
onEvent("restartButton", "click", function( ) {
  setScreen("startHomescreen");
  totalLives = 10;
  textBoxesshown = false;
  displayedIDSSum = 0;
  chosenWordAnswer = filteredAnswersList[(randomNumber(0, filteredAnswersList.length-1))];
  newGuesses = "Previous Guesses will show up here!";
  setAnswerTextBoxes();
  updateScreen(chosenWordAnswer);
});

//Determines if the guess is a character or word and runs the appropriate
//function
onEvent("guessButton", "click", function( ) {
  var inputtedGuess = getText("InputBox");
  if (inputtedGuess.length==1) {
    incremented = false;
    checkCharacters(chosenWordAnswer, inputtedGuess);
  } else {
    checkWords(chosenWordAnswer, inputtedGuess);
  }
});

//filters the "Words" datatable to only 5 letter nouns, and 
//assigns the chosen word to a variable 
var filteredAnswersList = [];
var partofSpeechList = getColumn("Words", "Part of Speech");
var answerWordList = getColumn("Words","Word");
for (var i = 0; i <  answerWordList.length; i++) {
  if (partofSpeechList[i] == "noun" && (answerWordList[i]).length == 5) {
  appendItem(filteredAnswersList,answerWordList[i]);
  }
}
var chosenWordAnswer = filteredAnswersList[(randomNumber(0, filteredAnswersList.length-1))];

//sets the text of the 5 text boxes to be the characters of the word 
//and hides those elements 
function setAnswerTextBoxes() {
  setText("characterAnswer1", chosenWordAnswer.substring(0,1));
  setText("characterAnswer2", chosenWordAnswer.substring(1,2));
  setText("characterAnswer3", chosenWordAnswer.substring(2,3));
  setText("characterAnswer4", chosenWordAnswer.substring(3,4));
  setText("characterAnswer5", chosenWordAnswer.substring(4,5));
  hideElement("characterAnswer1");
  hideElement("characterAnswer2");
  hideElement("characterAnswer3");
  hideElement("characterAnswer4");
  hideElement("characterAnswer5");
}
setAnswerTextBoxes();
//traverses through the chosen word using the inputted character to check
// if the characrter is in the list; if it is the element is shown
function checkCharacters(wordAnswer, character) {
  for (var i = 0; i < wordAnswer.length; i++) {
    var splitWord = wordAnswer.substring(0,wordAnswer.length);
    if (splitWord[i] == character) {
    var answertextBoxes = "characterAnswer" + (i+1);
    showElement(answertextBoxes);
    displayedIDSSum = i+1 + displayedIDSSum;
    incremented = true;
    updateScreen(wordAnswer);
    } else if (i == wordAnswer.length-1 && incremented == false) {
      totalLives = totalLives - 1;
      updateScreen(wordAnswer);
      i = wordAnswer.length;
    }
}
}
//checks if the guessed word is the correct answer, the chosen word,
function checkWords(wordAnswer, guessedWord) {
  if (wordAnswer == guessedWord){
  showallTextBoxes();
  updateScreen(wordAnswer);
  } else {
    totalLives = totalLives - 1;
    updateScreen(wordAnswer);
  }
}
//shows all 5 text boxes containing the characters for the word
function showallTextBoxes() {
  showElement("characterAnswer1");
  showElement("characterAnswer2");
  showElement("characterAnswer3");
  showElement("characterAnswer4");
  showElement("characterAnswer5");
  textBoxesshown = true;
}
//updates the screen when new guesses are inputed, changes the 
//life count,and ends the game when all lives are used
function updateScreen(wordAnswer) {
  newGuesses = (newGuesses + "\n");
  newGuesses = (((newGuesses)+ " ") + getText("InputBox"));
  setText("previousGuessBox", newGuesses); 
  setText("InputBox", "");
  setProperty("livesBox", "text", "Lives Left:" + totalLives);
  if (totalLives <=0 || textBoxesshown == true || displayedIDSSum == 15) {
    setScreen("gameOverscreen");
    setProperty("finalWordBox", "text", "The Word Was:" + "\n" + wordAnswer);
  }
}


