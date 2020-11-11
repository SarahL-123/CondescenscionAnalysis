# Condecenscion Detection

---

# Summary

Condecenscion is defined as "an attitude of patronizing superiority; disdain". It is also insulting, and so we would like to create a model that can detect it automatically (to reduce the amount of work that a human has to do). I also tried to research condecenscion but wasn't able to find a lot, so another goal of this project is to try and quantify condescending behavior.

The corpus of text I am using is a labelled set of 5000 Reddit post & reply pairs, from researchers Zijian Wang and Christopher Potts. The posts were sorted into 'condescending' and 'not condescending' by human readers.

The first thing that I did after cleaning the data was to use a simple model to try and predict condecenscion. This was reasonably effective, and we were able to find some things:
- The best way to predict if someone is condescending, is if the reply contains the word 'condescending' or an insulting word (e.g. f\*\*k)
- Without looking at the reply, simple models like logistic regression have a hard time determining whether text is condescending or not.

Based on these findings, I decided to use a pre-trained language model (BERT) to analyze the meanings/embeddings of the post and reply. It turns out that when someone is condescending, replies to them will have a noticeable change in the topic, so this is another good way to detect condecenscion (if there is a reply).

In addition, we found that sentiments commonly associated with being condescending also show up a lot in **replies** to the condescending post, suggesting that if someone is condescending, the reply will be even more condescending.

Finally, I tried to group condescending posts/replies into groups but it seems that there aren't any clear groupings.

Based on the above, we were able to find more things about condescenscion.
- If someone is condescending, this means it's more likely that the reply is also condescending. This means that condecenscion is a reply to other people being condescending (i.e. it's a vicious cycle)
- There only seems to be one category of condescension (wasn't able to group it into different categories)

Finally, I tried to use transfer learning + BERT to see if a model can detect condescending text without looking at the reply. This has around 70% AUC ROC which is better than the other models tried previously. However it still ends up with a good amount of false negatives.

In conclusion, if we have access to the reply we can predict if someone is being condescending quite accurately. However without the reply (the more general case) it is harder. We were able to identify some features of condescending text such as that it is a vicious cycle and also certain words that are more condescending (such as "lol", "sell", and "obvious").

---

## Contents
1. Introduction and EDA
2. Logistic Regression and Analysis
3. Topic Modelling and Unsupervised Learning
4. BERT
5. Conclusion

---

# Problem Statement
Condescenscion is a difficult thing to quantify. A general definition is "an attitude of patronizing superiority; disdain" (Oxford Languages). However, it's hard to pinpoint exactly which factors make someone's words condescending, and there isn't much research in this area too.

Based on this, I would like to do 2 things:
1) Try to identify common behaviors among condescending speech
2) Create a model that can identify someone who is being condescending.

To explain more about the model, I want to create a binary classification model that detects if someone is being condescending in Reddit posts. The performance is determined by the accuracy and AUC/ROC curve.

The model will classify a post as either condescending or not, ideally with a probability attached to this classification. The exact type of model depends on what works best, although it could range from a simple logistic regression all the way to a neural network.

I will judge the performance of the model using accuracy: this is because a balanced training set is provided in the data. However, I will also use the model on imbalanced data (to simulate actual Reddit posts), and for this I will use ROC AUC metric as well.

### Why do we want to know this?
It's difficult for traditional models to detect condescenscion since it depends on the context. This means that moderators of these posts have to manually look through each post and decide themselves, which is time consuming and maybe subjective to individual biases.

Therefore if a model is able to predict this, it will save a lot of time. Depending on how the model functions it may also be able to detect other 'bad' behavior such as being threatening or dismissive.

---

# Conclusion
There were 2 main aims of this project
1. Try and quantify what it means to be condescending
2. Make a model that can identify whether someone is being condescending.

---

For the first goal, we were able to find several interesting factors that can help a computer determine what condecenscion is. The first thing that I looked at was what words are commonly found to be condescending. However, this ended up not working as expected because the majority of condescenscion related words are all in the reply. Basically, when someone is condescending, the best way to tell this is if the **reply** contains the word 'condescending' or some kind of swear word/insult.

Based on this, we could get a 76% accuracy (for balanced classes) prediction using a logistic regression. However this relies on there being a reply, which is not always possible. Trying to predict condescenscion by only using the original text (no reply) is not accurate at all.

---

From above, it looks like people who are condescending ended up derailing the conversation. By using BERT to generate embeddings for both the post and the reply, we were able to get a vector representing the topics of each of them. Comparing these vectors shows us a few things:
- Posts who are condescending tend to have condescending replies too (note: for the purposes of our predictive model this doesn't matter, this is more aimed towards getting insight into condescenscion)
- Condescending posts create a change in the topic of the conversation, this can be seen by plotting the cosine similarity of the topics. Perhaps this could be a way to measure *how* condescending the post is, but a lot more analysis needs to be done to ascertain this.
- I found earlier that replies to condescending posts were either 1) insults or 2) commenting on the original post, for example "Your post was so ___". Using unsupervised learning I tried to group these replies into several categories but was unable to find groups.
- By putting these embeddings into a model we can get about 78% AUC ROC so we can see that just using the embedding works well.

Most of this needs a reply to the original post so it's not that useful in all cases. However we were able to find out some information about how people reply to posts that are condescending.

---

Finally, I tried using BERT + transfer learning to predict condecenscion using only the post. This is more geared towards the second aim of the project, since I would like to detect condescenscion without having to have someone reply.

On the other hand, this doesn't really help with the first aim of the project since BERT is a neural net and I can't look at coefficients to see what is going on.

In any case this method got an AUC ROC of about 70% (on imbalanced classes although that shouldn't matter). This is a lot better than the original logistic model of only 57% so it's definitely an improvement.

---

In conclusion, we were able to create a somewhat accurate model for detecting condescenscion without having to have someone reply. 



---

# Data Source/Dictionary
A labelled corpus of about 5000 Reddit posts, consisting of a post and a reply.

[Linnk](https://github.com/zijwang/talkdown)

    @inproceedings{wang2019talkdown,
      author = {Wang, Zijian  and  Potts, Christopher}
      title = {{TalkDown}: A Corpus for Condescension Detection in Context},
      booktitle = {Proceedings of the 2019 Conference on Empirical Methods in Natural Language Processing and the 9th International Joint Conference on Natural Language Processing},
      url = {https://www.aclweb.org/anthology/D19-1385},
      year = {2019}
    }


