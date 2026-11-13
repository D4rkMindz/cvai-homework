# Project Idea: Chess Board State from a God Cam

## Question

How reliably can classical computer vision recognise the board state (occupancy and piece type) from handheld top-down photos of a chess game?

## Goal

We build an algorithm that reads top-down ("god cam", bird's-eye view) photos of a chess board. For each of the 64 squares (A1–H8) it outputs:

1. whether a piece stands on the square (**occupancy**)
2. which **piece type** it is (king, queen, rook, bishop, knight, pawn; white or black)

### Stretch goals

- **Move table:** derive the move between two consecutive photos and log all moves in a table in standard chess notation.
- **Video:** process a video recording instead of single photos.

## Setup

| Aspect | Fact |
|---|---|
| Camera | We hold the camera by hand above the board. Position and angle shift a little between photos. |
| Sets | 2 wooden sets and 1 crystal set. We treat all three as equal datasets. |
| Data | Our own photos, about 10 per set. |
| Lighting | We vary it on purpose. |
| Data folders | `data/chess/<set>/` (e.g. `wood-small`, `wood-large`, `crystal`) |

### Photo sequence per set

1. **Empty board**: our calibration image and background.
2. **Start position**: all pieces in the base setup.
3. **One photo after each move** of the game.

## Method

We use classical computer vision only (Python + OpenCV in Jupyter), based on the lecture material. No deep learning.

1. **Find the board and squares:** locate the 64 squares on the empty-board photo, with contrast detection from Joachim Wirth's lecture notes or with SIFT.
2. **Background subtraction:** subtract the empty board from each photo with pieces.
3. **Occupancy:** decide which squares hold a piece.
4. **Piece type:** combine two sources.
   - **Visual:** recognise each piece by its top-down appearance. The start-position photo shows every piece on a known square, so it gives us labelled reference examples for each set.
   - **Move logic:** the start position is the known first state. Each later photo shows one more move, so the occupancy changes between two photos narrow down which piece moved.
   - In the start position, the queen stands on her own colour (white queen on a light square, black queen on a dark one). That rule separates king from queen.

## Evaluation

We label about 30 photos (3 sets × ~10) and compare the recognised board state with the true one:

- **Per-square accuracy** (main metric): occupancy and piece type, with a confusion matrix.
- **Full-board accuracy** (secondary): share of photos with all 64 squares correct.

## Open points

- **Alignment:** we have not decided how to align each handheld photo to the empty-board image (contrast detection or SIFT feature matching).
- **Handheld camera:** the camera moves between shots, so background subtraction needs an aligned photo first.
- **Varying lighting:** brightness can differ between the empty-board background and the photos with pieces.
