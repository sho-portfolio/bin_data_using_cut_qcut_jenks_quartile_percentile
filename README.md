# bin_data_using_cut_qcut_jenks_quartile_percentile
using python to bin data using cut, qcut and jenks optimization (quartile, percentile etc)


```python

# Three tdifferent functions to bucketize data
  # qcut - split observations (data) into equal sized bins (regrdless of the values)
  # cut - split observations into dispersed bins based on value of the observation (bins are created on the range of the values of the observations) - some bins can have zero observations and a bin can most of the observations for instance
  # jenks optimization - identify natural groupings of numbers that are “close” together while also maximizing the distance between the other groupings (bins) - tbd but may be similar to cut except that it will put observations into as many bins as specified (whereas cut can have empty bins)

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


