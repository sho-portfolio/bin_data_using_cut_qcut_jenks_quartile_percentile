# bin_data_using_cut_qcut_jenks_quartile_percentile
using python to bin data using cut, qcut and jenks optimization (quartile, percentile etc)


```python


# Three tdifferent functions to bucketize data
  # qcut - split observations (data) into equal sized bins (regrdless of the values)
  # cut - split observations into equally dispersed bins (ie use the values)
  # jenks optimization - split the observations into natural breaks

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

# show observations as a histograph (need to convert to a dataframe first)
pd.DataFrame(a).plot(kind='hist')
plt.show()

# show summary statistics of observations (need to convert to dataframe first)
print(pd.DataFrame(a).describe(), '\n')

# use qcut to bin observations into 4 bins and 10 bins (tries to distribute number of observations equally into each bin)
qcut_04 = pd.qcut(a, q=4, labels=[1,2,3,4], retbins=True)
qcut_10 = pd.qcut(a, q=10, labels=[1,2,3,4,5,6,7,8,9,10], duplicates='drop', retbins=True)

# use cut to bin observations into 4 bins and 10 bins (will put observations in bins based on the value of the observation - some bins can have no observations or one bin can have them all for instance)
cut_04 = pd.cut(a, bins=4, labels=[1,2,3,4], retbins=True)
cut_10 = pd.cut(a, bins=10, labels=[1,2,3,4,5,6,7,8,9,10], duplicates='drop', retbins=True)

# use jenks optimization to bin observations into 4 bins and 10 bins (creates natural breaks (bins))
breaks_04 = jenkspy.jenks_breaks(a, nb_class=4)
jenks_04 = pd.cut(a, bins=breaks_04, labels=[1,2,3,4], include_lowest=True, retbins=True)

breaks_10 = jenkspy.jenks_breaks(a, nb_class=10)
jenks_10 = pd.cut(a, bins=breaks_10, labels=[1,2,3,4,5,6,7,8,9], include_lowest=True, duplicates='drop', retbins=True)


# display the bins computed by qcut for 4 and 10 bins
print("qcut_04 bins", qcut_04[1])
print("qcut_10 bins", qcut_10[1], '\n')

# display the bins computed by cut for 4 and 10 bins (had to format numbers to nts to make them readable)
print("cut_04 bins", cut_04[1].astype(int))
print("cut_10 bins", cut_10[1].astype(int), '\n')

# display the bins computed by jenks for 4 and 10 bins
print("jenks_04 bins", jenks_04[1])
print("jenks_10 bins", jenks_10[1], '\n')


# create a dataframe and put all data side by side to see how the observations have been binned by qcut/cut/jenks
df = pd.DataFrame(data=a, columns=['score'])
df['qcut_04'] = qcut_04[0]
df['qcut_10'] = qcut_10[0]
df['cut_04'] = cut_04[0]
df['cut_10'] = cut_10[0]
df['jenks_04'] = jenks_04[0]
df['jenks_10'] = jenks_10[0]

display(df)

# note that jenks_10 looks silly but thats becuase we forced it create 10 bins, 
# when we asked it to create 4 bins it looks a lot better

```

