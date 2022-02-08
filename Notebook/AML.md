# Day-1

* Trends in ML: 

  * Deep Learning
    * text
    * Translation
    * Picture/music
  * Transfer Learning
  * Appiled ML: ensembled model

* ML in industry
  * feature engineering
  * recommender system
  * Models
    * not just building mdels
    * http://www.slideshare.net/SessionsEvents/3-ewa-dominowska-managing-machine-learning-projects-in-industry
      * where the data come from - the whole pipeline
  * content of this course
    * Machine Learning: A Probabilistic Perspective. Kevin P. Murphy

* Recommendation Systems

  * **Required video:**

    ● https://www.youtube.com/watch?v=zzTbptEdKhY **Optional**:

    ● https://engineering.quora.com/Machine-Learning-at-Quora
     ● https://www.youtube.com/watch?v=bLhq63ygoU8
     ● Chapter 9 from “Mining of Massive Datasets". Jure Leskovec, Anand Rajaraman and Jeffrey D. Ullman

  * Motivation
  * value
  * Type
    * Content-based: item in it
    * Collaborative filtering about neighbors
      * similarities + former user behavior
      * need extra data - type of feedback: explicit/implicit

  * elements
    *  user item, context, interface
    * User, item, utility matrix(sparse)
    * Architecture
  * collabrative filtering
    * memory based methods - KNN
      * user based
      * item based
      * measure: correlation/distance
    * model based methods



# Day-2

* Finish Matrix factorization
  * Gradient descent with momentum
  * Regularization

- Implicit Feedback
- Content based Recommendations
  - Item profile
    - music, movie, document
    - similarity measurement
      - jaccard
      - cosine
      - Pearson
      - Embedding neighbor
        - but expensive
        - hashing to small group 
  - user profile

* Recommendation as classification ○ 
  * Case Study: YouTube

* Intro to PyTorch. Matrix factorization with PyTorch.



# Day-3

- Summary of recommendation systems so far

  - Content based systems: 
    - features are based on content
  - Collaborative filtering systems: 
    - behavioral data about users (rating, buying, watching) items

  - Collaborative filtering systems:
    - KNN (memory based)
    - Matrix factorization (model based)
    - Data: utility matrix
  - Collaborative filtering systems type of data: 
    - explicit and implicit
    - **negative sampling**
  - Collaborative filtering systems **predict ratings**
- How to make recommendations?

  - Option 1: predict rating all unseen
    - Can be expensive - all
  - Option 2: similay item she saw recently
    - embedding item at the very beginning 
- Pros/Cons of content base recommendation

  - good 
    - no need for more datas
    - no cold-start problem on new item
    - easy to explain: just content
  - Bad:
    - domain knowledge for feature vectors
    - new genre
    - Some kind of items are not amenable to easy feature extraction (movies, music)
    - quality: this book are too basic, though content is ok
- Cold start problem

  - new user
    - popular thing
    - ask for more info - tags
    - social network assumption
  - new item - content
- YouTube case study

  - 3 stages 100/user => small set => On time refresh, diversify
- pytorch
  - tensors
  - gpu
  - autograd
  - Libraries

# day 4

* general pytorch

  * Steps:

  * tensor(x.values)

    * unsqueese(1)

  * Detach()

    * Model(x.float())

  * Data loader:

  * generate data:

    * Class RegressionDataset()

  * Data loader

    ```python
    from torch.utils.data import Dataset, DataLoader
    train_dl = DataLoader(fake_train_ds, batch_size=1000, shuffle=True)
    valid_dl = DataLoader(fake_valid_ds, batch_size=1000, shuffle=False)
    
    x, y = next(iter(train_dl)) # next batch
    ```

  * Batching data

    * Why? data is bigger than memory; gradient noise since small set not whole

  * Shuffling data: so won't all 1 or 0

  * Load the data in parallel using multiprocessing workers.

  * loss

    ```python
    from sklearn.metrics import r2_score
    
    def val_metric(model, valid_dl):
        model.eval()
        losses = []
        y_hats = []
        ys = []
        for x, y in valid_dl:
            y = y.unsqueeze(1)
            y_hat = model(x.float()) #x.float()
            loss = F.mse_loss(y_hat, y.float())
            y_hats.append(y_hat.detach().numpy	()) # y.detach().numpy()
            ys.append(y.numpy()) 
            losses.append(loss.item()) # .item()
        
        ys = np.concatenate(ys) # extend
        y_hats = np.concatenate(y_hats)
        return np.mean(losses), r2_score(ys, y_hats)
      
    # be carefull when log loss = 0.69
    ```

  * train

    ```python
    ## train_loop function
    def train_loop(model, train_dl, valid_dl, optimizer, epochs):
        for i in range(epochs):
          	losses = []
            model.train() # be careful where the model.train() is
            for x, y in train_dl:
                y = y.unsqueeze(1)
                y_hat = model(x.float())
                loss = F.mse_loss(y_hat, y.float())
                optimizer.zero_grad()
                loss.backward()
                optimizer.step()
                losses.append(loss.item())
            
            train_loss = np.mean(losses)
            valid_loss, valid_auc = val_metric(model, valid_dl)
            print("train loss %.3f valid loss %.3f auc roc %.3f" % (train_loss, valid_loss, valid_auc))
    ```

    * for each batches, the surface of bowl changes and the minimum is not a single point, it changes in each batch but in this case is more generalizable
    * Oscillating around the bottom

* Matrix factorization with PyTorch

  * Encoding data

    * 

  * Embedding layer

    * encode user and items into vectors

      ```python
      embed = nn.Embedding(10, 3)
      embed.weight # now is random
      ```

    * 'lookup' exactly from embed.weight with index

      ```python
      a = torch.LongTensor([[1,0,1,4,5,1]]) #[1,0,...] these are index inside embed.weight
      embed(a)
      ```

    * ```python
      class MF(nn.Module):
          def __init__(self, num_users, num_items, emb_size=100):
              super(MF, self).__init__()
              self.user_emb = nn.Embedding(num_users, emb_size)
              self.item_emb = nn.Embedding(num_items, emb_size)
              
              self.user_emb.weight.data.uniform_(0,0.05)
              self.item_emb.weight.data.uniform_(0,0.05) # initlializing weights
              
          def forward(self, u, v): # MF(x) called this method
              u = self.user_emb(u) # find the embedded value for user
              v = self.item_emb(v)
              return (u*v).sum(1) 
            	# element wise multiplication
           	 	# sum(1), 1 is the axis = 1
      ```

  * Debugging MF model

  * Training MF model

    ```python
    # not using data loaders because our data fits well in memory
    def train_epocs(model, epochs=10, lr=0.01, wd=0.0):
        optimizer = torch.optim.Adam(model.parameters(), lr=lr, weight_decay=wd) 
        # wd => L2 regularization \lambda in the loss function
        # in the gradient it is (dL/du + 2·lambda·u)
        for i in range(epochs):
            model.train()
            users = torch.LongTensor(train.userId.values)  #.cuda()
            items = torch.LongTensor(train.movieId.values) #.cuda()
            ratings = torch.FloatTensor(train.rating.values)  #.cuda()
        
            y_hat = model(users, items)
            loss = F.mse_loss(y_hat, ratings)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            testloss = valid_loss(model)
            print("train loss %.3f valid loss %.3f" % (loss.item(), testloss)) 
    ```

    

  * MF with bias

    ```python
    class MF_bias(nn.Module):
        def __init__(self, num_users, num_items, emb_size=100):
            super(MF_bias, self).__init__()
            self.user_emb = nn.Embedding(num_users, emb_size)
            self.user_bias = nn.Embedding(num_users, 1) # bias term
            self.item_emb = nn.Embedding(num_items, emb_size)
            self.item_bias = nn.Embedding(num_items, 1)
            # init 
            self.user_emb.weight.data.uniform_(0,0.05)
            self.item_emb.weight.data.uniform_(0,0.05)
            self.user_bias.weight.data.uniform_(-0.01,0.01)
            self.item_bias.weight.data.uniform_(-0.01,0.01)
            
        def forward(self, u, v):
            U = self.user_emb(u)
            V = self.item_emb(v)
            b_u = self.user_bias(u).squeeze()
            b_v = self.item_bias(v).squeeze()
            return (U*V).sum(1) +  b_u  + b_v
    ```

  * Lab

    * Can we change the first model to predict numbers in a particular range? Hint: sigmoid would create numbers between 0 and 1. Would this improve the model? `4*sigmoid(x)+1 => [1,5]` **since it the rating thing**=> working on the loss and then changes everything
    * Would a different Loss function improve results? What about absolute value instead of F.mse_loss? `l1_loss`















