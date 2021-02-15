##### Intro:

In this small project, I build a Sudoku solving application in Streamlit. We
train a computer vision model to recognize numbers in the 9x9 grid and use a backtracking algorithm to solve the actual puzzle.


##### How to Run
```
bash run_app.sh
```

##### Digit Detection

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

I generated a synthetic dataset to train the detection and recogntion models 
since it does not cost much and hope it works 😉.

To have a realistic data-set we use multiple types of fonts, sizes, background
colors, grid elements …

![](https://cdn-images-1.medium.com/max/800/1*cXmWQWiVwx779lm9EKfYig.png)

<span class="figcaption_hack">Example of generated Image</span>

Since we generate those examples from scratch, we can get all the details about
the position and the class of each digit in the image.

![](https://cdn-images-1.medium.com/max/800/1*CfQT1X4cxMK1eqnJq8ZbbA.png)

<span class="figcaption_hack">Final OCR result</span>

### Backtracking

We will use backtracking to solve the Sudoku. This method allows us to
step-by-step build candidate solutions in a tree-like shape and then prune this
tree if we find out that a sub-tree cannot yield a feasible solution.

The way we will do it in the case of Sudoku is as follows :

* For each cell, we compute the possible values that can be used to fill it given
the state of the grid. We can do this very easily by elimination.
* We sort the cells by their number of possible values, from lowest to greatest.
* We go through the first unfilled cell and assign it one of its possible values,
then to the next one and so on …
* if we end up we a feasible solution we return it, else we go back to the last
cell we assigned a value to and change its state to another possible value.
Kinda like depth-first tree search.

![](https://cdn-images-1.medium.com/max/800/1*SEoISyrZa_RexSPhmt2w_A.png)

<span class="figcaption_hack">Numbers define the order to traversal. Source:

[https://commons.wikimedia.org/wiki/File:Depth-first-tree.svg](https://commons.wikimedia.org/wiki/File:Depth-first-tree.svg)</span>

If after exploring all the possible leaves of this tree we can’t find a solution
then this Sudoku is unsolvable.

The advantage of backtracking is that it is guaranteed to find a solution or
prove that one does not exist. The issue is, while it is generally fast in 9x9
Sudoku grids, its time complexity in the general case is horrendous.

Implementation ( Some operations, like sorting, are performed in the “Board”
class):

    def backtracking_solve(board):
        # Modified from 
        set_initially_available(board.cells)
        to_be_filled = board.get_unused_cells()
        index = 0
        n_iter = 0
        while -1 < index < len(to_be_filled):
            current = to_be_filled[index]
            flag = False
            possible_values = board.get_possibles(current)
            my_range = range(current.value + 1, 10)
            for x in my_range:
                if x in possible_values:
                    n_iter += 1
                    current.value = x
                    flag = True
                    break
            if not flag:
                current.value = 0
                index -= 1
            else:
                index += 1
        if len(to_be_filled) == 0:
            return n_iter, False
        else:
            return n_iter, index == len(to_be_filled)

### The App

We build the app using Streamlit. The app needs to allow us to upload an image,
solve the Sudoku, and display the results.

#### File Upload :

Streamlit provides a simple way to create a file upload widget using
st.file_uploader.

    file = st.file_uploader("Upload Sudoku image", type=["jpg", "png"])

#### OCR :

We apply the detector and recognizer model to create the grid.

    grid = img_to_grid(img, detector_model, recognizer_model, plot_path=None, print_result=False)

#### Solving :

We use backtracking to solve the Sudoku.

    n_iter, _ = backtracking_solve(to_solve_board)

#### Display the results :

We Display the results in a nice looking Html/Css table by specifying
unsafe_allow_html=True.

    html_board.markdown("<center>" + to_solve_board.html() + "</center>", unsafe_allow_html=True)

#### Final result :

![](https://cdn-images-1.medium.com/max/2560/1*v1bArKhF6rA0KvMxRfUg1g.png)



References :
[https://github.com/RutledgePaulV/sudoku-generator](https://github.com/RutledgePaulV/sudoku-generator)

Cite:
```
@software{mansar_youness_2020_4060213,
  author       = {Mansar Youness},
  title        = {CVxTz/sudoku\_solver: v0.3},
  month        = sep,
  year         = 2020,
  publisher    = {Zenodo},
  version      = {v0.3},
  doi          = {10.5281/zenodo.4060213},
  url          = {https://doi.org/10.5281/zenodo.4060213}
}
```
