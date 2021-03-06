## Long Short Term (LSTM) Recurrent Neural Net (RNN) trained for classifying obfuscated sentences from twelve different novels

### Baseline Machine Learning approach (Logistic Regression, Support Vector Machine), beating the baseline with Neural Net (LSTM-RNN)

by Dr. Hannah Bohle, 09.09.2020

### Table of Content:

    - loading data as df
    - Exploratory Data Analysis of training and test data
    - Defining train_test_split from "train"
    - Baseline Model (Logistic Regression, SVM)
    - Neural Net: Long-Short-Term Memory (LSTM) Recurrent Neural Net (RNN)
    - Predicting labels of "test" data

### Task:

Model is expected to classify a given line as belonging to one of the following 12 novels. The sentences of the novels have been obfuscated, however the patterns in them are preserved.

Obfuscated text example: 0: satwamuluhqgulamlrmvezuhqvkrpmletwulcitwskuhlejfjaskljfsklajeifuweiondov

Sentences were subtracted from the following novels: 

0: alice_in_wonderland 1: dracula 2: dubliners 3: great_expectations 4: hard_times 5: huckleberry_finn 6: les_miserable 7: moby_dick 8: oliver_twist 9: peter_pan 10: talw_of_two_cities 11: tom_sawyer

Because of the obfuscation, text preprocessing steps such as stopword removal, lemmatization, tokenization, removal of noise (tags, number, whitespaces..) do not apply. 

### Procedure:

To construct a baseline, I trained a Logistic Regression and a SVM. In a next step, I constructed a Long Short Term Memory (LSTM) Recurrent Neural Net (RNN).

### Exploratory Data Analysis:

The quick EDA shows that the data set is imbalanced, number of available sequences is ranging from 500 char sequences (Alice in Wonderland) to 5000 char sequences (Moby Dick). Therefore, in addition to plotting the confusion matrix, I decided to observe the f1-score of the baseline models.

### Results: 

While simple machine learning algorithms such as Support Vector Machines achieve an f1-score and accuracy of 0.34. A deep learning algorithm achieves 0.85 Hence, it performs much better. In this case, a Recurrent Neural Net (RNN) Long-Short-Term (LSTM) Neural Net was used.

As a baseline, a Logistic Regression and a Support Vector Machine were trained. Because of the obfuscation, only the frequency of the letters could be used ( = Bag of Words CountVectorizer on char level). As we see, the f1-score was very different for the 12 books. The algorithm worked well on two of the books, #5: huckleberry_finn and #8: oliver_twist. However, the overall f1-score was around 0.38 for both models. In my opinion, this is not even a bad score, considering that the algorithm could only use the letter frequency for its prediction. 

Out of the box, the LSTM Recurrent Neural Net shows promising results with an accuracy of about 0.61. However, after 13 epochs it started to overfit. Therefore, in a next step I trained a model while implementing Early Stopping to prevent overfitting. The highest accuracy I received with this last model was around 0.85 (Early Stopping, restoring best weights).

## LSTM Recurrent Neural Net
```python
# defining LSTM with activation function Softmax due to multi-class labeling; dense 12 because of 12 classes
embed_dim = 128
lstm_out = 200
batch_size = 32

model = Sequential()
model.add(Embedding(30, embed_dim,input_length = train_data.shape[1]))
model.add(LSTM(lstm_out))

#sparse categorical cross entropy: the labels are mutually exclusive for each data, meaning each data entry can only belong to one class.
model.compile(loss='sparse_categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
```
# second model trained - with early stopping
```python
early_stopping_cb = keras.callbacks.EarlyStopping(patience=10,restore_best_weights=True)
checkpoint_cb = keras.callbacks.ModelCheckpoint("LSTM_with_EarlyStopping.h5",save_best_only=True)

num_epochs = 100
history2 = model2.fit(train_data, train_classes, epochs=num_epochs, validation_data=(val_data, val_classes), verbose=2,callbacks=[checkpoint_cb,early_stopping_cb])
```


