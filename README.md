## Protease kinetics as weighted sums of continued fraction of linear dynamics  

### The model is explained here in this [preprint](https://chemrxiv.org/engage/chemrxiv/article-details/67ef642bfa469535b9adb5d7): Analyzing enzyme kinetics using a growth model

### Abstract  
Dynamics of population growth or enzyme kinetics do not follow a constant rate making them difficult to quantify. Therefore, a single measure of dynamics is desired to profile the kinetic activities of enzymes with respect to various substrates. In this work, we use a growth model to model enzyme kinetics and provide a measure for kinetic activity based on the model parameters. This growth model, named the a − m model, has been fitted on nonlinear growth data of populations including bacterial colonies and longitudinal growth data of a human male. We apply the a − m growth model to fit and interpret the publicly available protease kinetic data provided by the developers of the protease activity analysis(PAA) toolkit. The results are summarized and interpreted using a growth coordinate system on an xy− plane.  

Data for the following analysis is obtained from [here](https://github.com/avaamini/protease_activity_analysis/tree/master/data/stm_kinetic).

The pickle files for each protease is created with the following code
```rb
import pandas as pd
import numpy as np
from gen_curve_fitting import sup_fit,fn_c
import pickle
#We consider the mean of replicates
in_path = '/root/my-documents/protease_activity_analysis/data/stm_kinetic/MMP13_stm.xlsx'
raw = pd.read_excel(in_path, engine='openpyxl')
raw_mean=raw.groupby(raw.columns[0]).agg([np.mean])
raw_mean.columns=raw_mean.columns.droplevel(1)
fc=raw_mean.div(raw_mean[0], axis=0)
x=fc.columns.to_numpy(dtype=np.float64)
paramss=[]
yeval=np.empty(shape=(fc.shape[0],fc.shape[1]));
num_of_origins=3 #Number of additional origins
for i in range(fc.shape[0]):
    y = np.array(fc.iloc[i])
    yeval[i],params = sup_fit(x,y,num_of_origins)
    paramss.append(params)
#yeval is the fitted curve
with open('ypars_MMP13','wb') as handle:
    pickle.dump([yeval,paramss],handle,protocol=pickle.HIGHEST_PROTOCOL)
```

The pickle files are then read as
```rb
with open('ypars_MMP13', 'rb') as handle:
    out = pickle.load(handle)
yeval = out[0];pars = out[1]
#yeval is the fitted curve with parameters given by pars.
#$a$ and $m$ of the $a-m$ model is obtained as follows
for j in pars:
        m_list.append(np.max(fnder(x,j)))
        a_list.append(j['a'])
#m_list and a_list are later saved into mvals.csv and avals.csv respectively.
```
One can als fit any other data using 
```rb
yfit,params = sup_fit(xdata,ydata,num_of_origins)
```
This way we have fit various growth data:  
[A biological growth curve is a sum of two distinct S-curves](https://www.biorxiv.org/content/10.1101/2025.02.06.636984v2)  
[Modeling Montbeillard’s height data of a human male](https://www.biorxiv.org/content/10.1101/2025.03.02.641023v1)  
We have also compared models with different number of origins using a bacterial growth data [here](https://www.biorxiv.org/content/10.1101/2025.01.27.634991v2).
