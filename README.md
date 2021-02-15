#### Intro:

In this small project, I build a Sudoku solving application in Streamlit. We
train a computer vision model to recognize numbers in the 9x9 grid and use a backtracking algorithm to solve the actual puzzle.


#### How to Run
```
bash run_app.sh
```

#### Digit Detection

To solve the sudoku puzzle we need to build a digit detector model and then a digit recognizer
model. The detection model will tell us where a digit appears in the image and
the recognition model will tell us which digit it is. We will also generate a dataset for
both models.

#### Detection Model

The detector model is a fully convolutional neural network which outputs a binary mask 
predicting whether or not a pixel is apart of a digit.

#### Recognition Model

The recognition model is a convolutional neural network which has a fully connected output layer
with softmax activation predicting the probability of pixels belonging to each digit. 

#### Data-set

I generated a dataset to train the detection and recogntion models by using Paul Rutledge's
sudoku generating code located at [https://github.com/RutledgePaulV/sudoku-generator](https://github.com/RutledgePaulV/sudoku-generator). 
To improve the performance of both models I used multiple fonts and sizes.

#### Backtracking

The backtacking algotithm solves the sudoku puzzle once the location and values of all the 
digits in the puzzle are found.

The algorithm computes the possible values that can be used to fill in the grid given the 
current state, and then eliminates all of the solutions that do not comply with the rules 
(1-9 in each row, column, and 3x3 cell). As multiple solutions exist for some puzzles, the
first one the algorithm finds will be considered the output.

#### The App

The app was built using Streamlit which is a very simple python library that quickly allows the deployment
of python apps in the local browser. The app only requires streamlit to be installed locally and an image of 
a sudoku puzzle.

#### File Upload :

Streamlit provides a simple way to create a file upload widget using
st.file_uploader.

    file = st.file_uploader("Upload Sudoku image", type=["jpg", "png"])

#### Display the results :

The results are displayed in a table:

    html_board.markdown("<center>" + to_solve_board.html() + "</center>", unsafe_allow_html=True)

#### Final result :

An example of the final result:

![](https://cdn-images-1.medium.com/max/2560/1*v1bArKhF6rA0KvMxRfUg1g.png)



References :

[https://github.com/CVxTz/sudoku_solver](https://github.com/CVxTz/sudoku_solver)
[https://github.com/RutledgePaulV/sudoku-generator](https://github.com/RutledgePaulV/sudoku-generator)
