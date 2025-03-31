## Protease kinetics as weighted sums of continued fraction of linear dynamics

Data for the following analysis is obtained from [here](https://github.com/avaamini/protease_activity_analysis/tree/master/data/stm_kinetic).

The pickle files for each protease is created with the following code
```rb
import pandas as pd
import numpy as np
from gen_curve_fitting import sup_fit,fn_c
import pickle

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
We have also compared number of origins with a bacterial growth data [here](https://www.biorxiv.org/content/10.1101/2025.01.27.634991v2).
