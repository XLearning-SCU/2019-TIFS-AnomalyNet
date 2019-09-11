### SC2Net
If you feel this project helpful to your research, please cite the following two papers. 

Source codes for 

* **"SC2Net: Sparse LSTMs for Sparse Coding" (AAAI 2018)**

* **"An End-to-end Sparse coding" Presented at the ICML 2017 Workshop on Principled Approaches
to Deep Learning, Sydney, Australia, 2017**

This project includes all the baseline (i.e., ISTA, LISTA, FLISTA, LISTA-SC2Net, FLISTA-SC2Net) implementations reported in the paper. The Tensorflow based implementation code for SLSTM can be found in Lcod/sparse_lstm.py 

The tensorflow based implementaion code for baseline algorithms lista and lfista can be found in Lcod/lista_network.py Lcod/lfista_network.py. We modify the loss functions of LISTA and FLISTA (parts of codes from [Thomas Moreau github projects](https://github.com/tomMoral/AdaptiveOptim)) to fit the SC2Net framework.



### Requirement
 * numpy 1.10+
 * matplotlib 1.8+
 * tensorflow 1.0+
 * python 2.7
 * scikit-learn 1.16+
 

### Usage
To use sparse_lstm independently 
    from Lcod.SLSTM import SLSTM
    model = SLSTM(state_size = STATE_SIZE, num_classes=classes, num_layers=LAYERS)
    model.train(train_set)
The SLSTM.train method currently is for training specified dataset, a more general train method will be commited in few days. 
  
    
To use lista lfista independently

    from Lcod.lista_network import LIstaNetwork
    from Lcod.lfista_network import LFistaNetwork
Different from the sparse_lstm, lista and lfista require pre-learned dictionary to initialize weights, the dictionary is pre learned by sklearn.decomposition.DictionaryLearning.  

    lis = LIstaNetwork(D, n_layers=10, shared=True,supervised=False, log_lvl=log_lvl,gpu_usage=gpu_usage, exp_dir=NAME_EXP,feed_lmbd=lmbd,Zpflag = False)
    lfis = LFistaNetwork(D, n_layers=10, shared=True, supervised=False, log_lvl=log_lvl,gpu_usage=gpu_usage,exp_dir=NAME_EXP,feed_lmbd=lmbd,Zpflag = False)
    #D is the pre-learned dictionary
    
    lis.train(batch_provider=pb, max_iter=20, steps=steps, feed_val=feed_val_lista, reg_cost=8, tol=1e-8,lr_init=lr_init)
    lfis.train(batch_provider=pb, max_iter=20, steps=steps, feed_val=feed_val_lista, reg_cost=8, tol=1e-8,lr_init=lr_init)
    #pb is the dataset generator

To run mnist & cifar reconstruction test



    python figure.py --data mnist --lmbd .1 -K 100 --save_dir mnist
    python figure.py --data cifar --lmbd .1 -K 100 --save_dir cifar
    #--data determines the dataset to be trained, --K determines the output sparce code length.
    
    #To generate the reconstructed pic of mnist cifar, run the following codes,these codes are in line 295 to 
    # line 314 in figure.py script, reconstructed pics would be shown automatically, so that there may be some 
    # errors when using remote ssh connection. 
    
    re_lis = lis.reconstruct(pb,lmbd=lmbd)
    re_lfis = lfis.reconstruct(pb,lmbd=lmbd)
    from reconstruct import reconstrcuct_drow
    reconstrcuct_drow(pb,lis = re_lis,lfis=re_lfis)
    
    
    

To train customized dataset, you need to write a dataset generator class which is similar to Lcod/mnist_problem_generator.py, the generator should contain a class to generate data by batch and a method to generate the pre-learned dictionary. 

    from Lcod.mnist_problem_generator import MnistProblemGenerator
    from Lcod.mnist_problem_generator import create_dictionary_dl
    D = create_dictionary_dl(lmbd, K, N=10000, dir_mnist=save_dir)
    pb = MnistProblemGenerator(D, lmbd, batch_size=batch_size,
                                   dir_mnist=save_dir, seed=42242) 
### Visual Result
#### Mnist 
Reconstructed images on MNIST: The more black
is, the lower error is
![Mnist](pic/mnist.png?raw=true)

#### Cifar
Reconstructed images on CIFAR-10
![Cifar](pic/cifar.png?raw=true)

