# bin_data_using_cut_qcut_jenks_quartile_percentile
using python to bin data using cut, qcut and jenks optimization (quartile, percentile etc)


```python

# Three different functions to bucketize data
  # qcut - split observations (data) into equal sized bins (regrdless of the values)
    # - you specify the number of bins (q)
    # - qcut will create bins of equal size (i.e. bin1 0-2, bin2 2-4 etc)
    # - it may not create the number of bins you specify for q below will create only 9 bins
    #.     pd.qcut(x=[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 10], q=10, duplicates='drop')
 # cut - split observations into dispersed bins based on value of the observation
 # jenks optimization - split the observations into natural breaks

# cut vs qcut
   # cut chooses the bins to be evenly spaced according to the values themselves 
   # qcut chooses the bins so that you have the same number of records in each bin
    
# resources
  # https://pbpython.com/natural-breaks.html
  # https://pbpython.com/pandas-qcut-cut.html
  # https://stackoverflow.com/questions/30211923/what-is-the-difference-between-pandas-qcut-and-pandas-cut
  # https://towardsdatascience.com/sort-and-segment-your-data-into-bins-to-get-sorted-ranges-pandas-cut-and-qcut-7785931bbfde


import pandas as pd
import jenkspy
import matplotlib.pyplot as plt

# list of observations
a = [1000,500, 55,10,9,9,8,7,6,5,4,3,2,1,1]


import pandas as pd
import jenkspy
import matplotlib.pyplot as plt

# list of observations
a = [1000,500, 55,10,9,9,8,7,6,5,4,3,2,1,1]

# show observations as a histograph (need to convert to a dataframe first)
pd.DataFrame(a).plot(kind='hist')
plt.show()

# show summary statistics of observations (need to convert to dataframe first)
print(pd.DataFrame(a).describe(), '\n')

# use qcut to bin observations into 4 bins and 10 bins (tries to distribute number of observations equally into each bin)
qcut_04_res, qcut_04_bin = pd.qcut(a, q=4, labels=[1,2,3,4], retbins=True)
qcut_10_res, qcut_10_bin = pd.qcut(a, q=10, labels=[1,2,3,4,5,6,7,8,9,10], duplicates='drop', retbins=True)

# use cut to bin observations into 4 bins and 10 bins (will put observations in bins based on the value of the observation - some bins can have no observations or one bin can have them all for instance)
cut_04_res, cut_04_bin = pd.cut(a, bins=4, labels=[1,2,3,4], retbins=True)
cut_10_res, cut_10_bin = pd.cut(a, bins=10, labels=[1,2,3,4,5,6,7,8,9,10], duplicates='drop', retbins=True)

# use jenks optimization to bin observations into 4 bins and 10 bins (creates natural breaks (bins))
breaks_04 = jenkspy.jenks_breaks(a, nb_class=4)
jenks_04_res, jenks_04_bin = pd.cut(a, bins=breaks_04, labels=[1,2,3,4], include_lowest=True, retbins=True)

breaks_10 = jenkspy.jenks_breaks(a, nb_class=10)
jenks_10_res, jenks_10_bin = pd.cut(a, bins=breaks_10, labels=[1,2,3,4,5,6,7,8,9], include_lowest=True, duplicates='drop', retbins=True) 
# note that there are only 9 labels - had to do this to avoind error! (unsure why as of yet)


# display the bins computed by qcut for 4 and 10 bins
print("qcut_04 bins", qcut_04_bin)
print("qcut_10 bins", qcut_10_bin, '\n')

# display the bins computed by cut for 4 and 10 bins (had to format numbers to nts to make them readable)
print("cut_04 bins", cut_04_bin.astype(int))
print("cut_10 bins", cut_10_bin.astype(int), '\n')

# display the bins computed by jenks for 4 and 10 bins
print("jenks_04 bins", jenks_04_bin)
print("jenks_10 bins", jenks_10_bin, '\n')


# create a dataframe and put all data side by side to see how the observations have been binned by qcut/cut/jenks
df = pd.DataFrame(data=a, columns=['score'])
df['qcut_04'] = qcut_04_res
df['qcut_10'] = qcut_10_res
df['cut_04'] = cut_04_res
df['cut_10'] = cut_10_res
df['jenks_04'] = jenks_04_res
df['jenks_10'] = jenks_10_res

with pd.option_context('display.max_rows', None, 'display.max_columns', None):  # show the entire dataset
    display(df.sort_values(by='score'))

# note that jenks_10 looks silly but thats becuase we forced it create 10 bins, 
# when we asked it to create 4 bins it looks a lot better

```

<img src="https://user-images.githubusercontent.com/40234177/94735209-6277c380-0338-11eb-83ac-4fa58755951a.png" width=50% height=50%>



<h3> testing qcut </h3>
Results summary - with qcut, you can specify the number of bins you want but you can't guarentee that that is how many you will get

```python

#qcut takes your list of observations and will try to put an equal number of observations (x) into each bucket.
#you have to specify the number of buckets (q)


import pandas as pd

def build_output2(x, q, edge, bin, bin_label, note):
    
    print("-------------------------------------------------------------------------"'\n')

    df = pd.DataFrame(x, columns=['observation'])
    df['bin_label'] = bin_label
    df['edge'] = edge

    #df['obs_per_bin_label'] = pd.Series(bin_label).value_counts()
    #df['obs_per_bin_label'] = df['bin_label'].value_counts()  
    df['obs_per_bin_label']= df.bin_label.map(df.bin_label.value_counts())

    #display(pd.Series(bin_label).value_counts()) #shows how many observations were put into each bin

    #print(" observation-bin: ", out, '\n', "bin edges: ", bin, '\n', "bin count: ", len(bin)-1)
    print(" note:\n", note, "\n\n", "observations:\t", x, "\n", "bin edges:\t", bin, '\n', "bin count:\t", len(bin)-1, '\n', "bins reqstd:\t", q, '\n')

    display(df)
    print("-------------------------------------------------------------------------"'\n')

note = "[1] if you have 10 non-duplicate observations & you want to place them in 10 bins. qcut creates 10 bins and places an equal number of observations in each bin"
x = [1,2,3,4,5,6,7,8,9,10]
q=10
edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='raise')
bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='raise')
build_output2(x, q, edge, bin, bin_label, note)

#2
note = "[2] now what happens if we have more observations than bins. qcut puts 2 observations in one bin as its the best it can do (see bin 0)"
x = [1,2,3,4,5,6,7,8,9,10,11]
q=10
edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='raise')
bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='raise')
build_output2(x, q, edge, bin, bin_label, note)

#3
note = "[3] what happens when we have fewer observations than bins. qcut puts 1 observation in one bin but skips a bin (see bin 5!)"
x = [1,2,3,4,5,6,7,8,9]
q=10
edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='raise')
bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='raise')
build_output2(x, q, edge, bin, bin_label, note)

#4
try:
    note = "[4] what happens when we have a duplicate observation? We get an error!"
    x = [1,2,3,4,5,6,7,8,9,10,10]
    q=10
    edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='raise')
    bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='raise')
    build_output2(x, q, edge, bin, bin_label, note)
except Exception as e:
    print(" note:\n", note, '\n', " Error: ", e)
    
    
#4.1
note = "[4.1] so to fix it we add an additional paramter [duplicates='drop'].  notice that it used 9 bins only"
x = [1,2,3,4,5,6,7,8,9,10,10]
q=10
edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='drop')
bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='drop')
build_output2(x, q, edge, bin, bin_label, note)


#5
note = "[5] what happens when we have all observations with the same value (this is silly but whatever)"
x = [10,10,10,10,10,10]
q=10
edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='drop')
bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='drop')
build_output2(x, q, edge, bin, bin_label, note)


#6
note = "[6] how to qcut a a series/dataframe"
x = df['some_column_name'].tolist()
q=10
edge, bin = pd.qcut(x, q=q, retbins=True, duplicates='drop')
bin_label, bin_b = pd.qcut(x, q=q, labels=False, retbins=True, duplicates='drop')
build_output2(x, q, edge, bin, bin_label, note)

```


