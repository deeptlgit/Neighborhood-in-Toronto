# [Segmentation and Clustering](https://rawnote.dinhanhthi.com/files/ibm/neighborhoods_in_toronto)

Segmenting and Clustering Neighbourhoods in Toronto
The project includes scraping the Wikipedia page for the postal codes of Canada and then process and clean the data for the clustering. The clustering is carried out by K Means and the clusters are plotted using the Folium Library. The Boroughs containing the name 'Toronto' in it are first plotted and then clustered and plotted again.

All the 3 tasks of web scraping, cleaning and clustering are implemented in the same notebook for the ease of evaluation.
Installing and Importing the required Libraries
In [2]:
!pip install beautifulsoup4
!pip install lxml
import requests # library to handle requests
import pandas as pd # library for data analsysis
import numpy as np # library to handle data in a vectorized manner
import random # library for random number generation

#!conda install -c conda-forge geopy --yes 
from geopy.geocoders import Nominatim # module to convert an address into latitude and longitude values

# libraries for displaying images
from IPython.display import Image 
from IPython.core.display import HTML 


from IPython.display import display_html
import pandas as pd
import numpy as np
    
# tranforming json file into a pandas dataframe library
from pandas.io.json import json_normalize

!conda install -c conda-forge folium=0.5.0 --yes
import folium # plotting library
from bs4 import BeautifulSoup
from sklearn.cluster import KMeans
import matplotlib.cm as cm
import matplotlib.colors as colors

print('Folium installed')
print('Libraries imported.')
Requirement already satisfied: beautifulsoup4 in /opt/conda/envs/Python36/lib/python3.6/site-packages (4.7.1)
Requirement already satisfied: soupsieve>=1.2 in /opt/conda/envs/Python36/lib/python3.6/site-packages (from beautifulsoup4) (1.7.1)
Requirement already satisfied: lxml in /opt/conda/envs/Python36/lib/python3.6/site-packages (4.3.1)
Solving environment: done

## Package Plan ##

  environment location: /opt/conda/envs/Python36

  added / updated specs: 
    - folium=0.5.0


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    altair-3.1.0               |           py36_0         724 KB  conda-forge
    vincent-0.4.4              |             py_1          28 KB  conda-forge
    certifi-2019.6.16          |           py36_1         149 KB  conda-forge
    openssl-1.1.1c             |       h516909a_0         2.1 MB  conda-forge
    branca-0.3.1               |             py_0          25 KB  conda-forge
    folium-0.5.0               |             py_0          45 KB  conda-forge
    ca-certificates-2019.6.16  |       hecc5488_0         145 KB  conda-forge
    ------------------------------------------------------------
                                           Total:         3.2 MB

The following NEW packages will be INSTALLED:

    altair:          3.1.0-py36_0      conda-forge
    branca:          0.3.1-py_0        conda-forge
    folium:          0.5.0-py_0        conda-forge
    vincent:         0.4.4-py_1        conda-forge

The following packages will be UPDATED:

    ca-certificates: 2019.5.15-0                   --> 2019.6.16-hecc5488_0 conda-forge
    certifi:         2019.6.16-py36_0              --> 2019.6.16-py36_1     conda-forge

The following packages will be DOWNGRADED:

    openssl:         1.1.1c-h7b6447c_1             --> 1.1.1c-h516909a_0    conda-forge


Downloading and Extracting Packages
altair-3.1.0         | 724 KB    | ##################################### | 100% 
vincent-0.4.4        | 28 KB     | ##################################### | 100% 
certifi-2019.6.16    | 149 KB    | ##################################### | 100% 
openssl-1.1.1c       | 2.1 MB    | ##################################### | 100% 
branca-0.3.1         | 25 KB     | ##################################### | 100% 
folium-0.5.0         | 45 KB     | ##################################### | 100% 
ca-certificates-2019 | 145 KB    | ##################################### | 100% 
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
Folium installed
Libraries imported.
Scraping the Wikipedia page for the table of postal codes of Canada
BeautifulSoup Library of Python is used for web scraping of table from the Wikipedia. The title of the webpage is printed to check if the page has been scraped successfully or not. Then the table of postal codes of Canada is printed.

In [4]:
source = requests.get('https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M').text
soup=BeautifulSoup(source,'lxml')
print(soup.title)
from IPython.display import display_html
tab = str(soup.table)
display_html(tab,raw=True)
<title>List of postal codes of Canada: M - Wikipedia</title>
Postcode	Borough	Neighbourhood
M1A	Not assigned	Not assigned
M2A	Not assigned	Not assigned
M3A	North York	Parkwoods
M4A	North York	Victoria Village
M5A	Downtown Toronto	Harbourfront
M5A	Downtown Toronto	Regent Park
M6A	North York	Lawrence Heights
M6A	North York	Lawrence Manor
M7A	Queen's Park	Not assigned
M8A	Not assigned	Not assigned
M9A	Etobicoke	Islington Avenue
M1B	Scarborough	Rouge
M1B	Scarborough	Malvern
M2B	Not assigned	Not assigned
M3B	North York	Don Mills North
M4B	East York	Woodbine Gardens
M4B	East York	Parkview Hill
M5B	Downtown Toronto	Ryerson
M5B	Downtown Toronto	Garden District
M6B	North York	Glencairn
M7B	Not assigned	Not assigned
M8B	Not assigned	Not assigned
M9B	Etobicoke	Cloverdale
M9B	Etobicoke	Islington
M9B	Etobicoke	Martin Grove
M9B	Etobicoke	Princess Gardens
M9B	Etobicoke	West Deane Park
M1C	Scarborough	Highland Creek
M1C	Scarborough	Rouge Hill
M1C	Scarborough	Port Union
M2C	Not assigned	Not assigned
M3C	North York	Flemingdon Park
M3C	North York	Don Mills South
M4C	East York	Woodbine Heights
M5C	Downtown Toronto	St. James Town
M6C	York	Humewood-Cedarvale
M7C	Not assigned	Not assigned
M8C	Not assigned	Not assigned
M9C	Etobicoke	Bloordale Gardens
M9C	Etobicoke	Eringate
M9C	Etobicoke	Markland Wood
M9C	Etobicoke	Old Burnhamthorpe
M1E	Scarborough	Guildwood
M1E	Scarborough	Morningside
M1E	Scarborough	West Hill
M2E	Not assigned	Not assigned
M3E	Not assigned	Not assigned
M4E	East Toronto	The Beaches
M5E	Downtown Toronto	Berczy Park
M6E	York	Caledonia-Fairbanks
M7E	Not assigned	Not assigned
M8E	Not assigned	Not assigned
M9E	Not assigned	Not assigned
M1G	Scarborough	Woburn
M2G	Not assigned	Not assigned
M3G	Not assigned	Not assigned
M4G	East York	Leaside
M5G	Downtown Toronto	Central Bay Street
M6G	Downtown Toronto	Christie
M7G	Not assigned	Not assigned
M8G	Not assigned	Not assigned
M9G	Not assigned	Not assigned
M1H	Scarborough	Cedarbrae
M2H	North York	Hillcrest Village
M3H	North York	Bathurst Manor
M3H	North York	Downsview North
M3H	North York	Wilson Heights
M4H	East York	Thorncliffe Park
M5H	Downtown Toronto	Adelaide
M5H	Downtown Toronto	King
M5H	Downtown Toronto	Richmond
M6H	West Toronto	Dovercourt Village
M6H	West Toronto	Dufferin
M7H	Not assigned	Not assigned
M8H	Not assigned	Not assigned
M9H	Not assigned	Not assigned
M1J	Scarborough	Scarborough Village
M2J	North York	Fairview
M2J	North York	Henry Farm
M2J	North York	Oriole
M3J	North York	Northwood Park
M3J	North York	York University
M4J	East York	East Toronto
M5J	Downtown Toronto	Harbourfront East
M5J	Downtown Toronto	Toronto Islands
M5J	Downtown Toronto	Union Station
M6J	West Toronto	Little Portugal
M6J	West Toronto	Trinity
M7J	Not assigned	Not assigned
M8J	Not assigned	Not assigned
M9J	Not assigned	Not assigned
M1K	Scarborough	East Birchmount Park
M1K	Scarborough	Ionview
M1K	Scarborough	Kennedy Park
M2K	North York	Bayview Village
M3K	North York	CFB Toronto
M3K	North York	Downsview East
M4K	East Toronto	The Danforth West
M4K	East Toronto	Riverdale
M5K	Downtown Toronto	Design Exchange
M5K	Downtown Toronto	Toronto Dominion Centre
M6K	West Toronto	Brockton
M6K	West Toronto	Exhibition Place
M6K	West Toronto	Parkdale Village
M7K	Not assigned	Not assigned
M8K	Not assigned	Not assigned
M9K	Not assigned	Not assigned
M1L	Scarborough	Clairlea
M1L	Scarborough	Golden Mile
M1L	Scarborough	Oakridge
M2L	North York	Silver Hills
M2L	North York	York Mills
M3L	North York	Downsview West
M4L	East Toronto	The Beaches West
M4L	East Toronto	India Bazaar
M5L	Downtown Toronto	Commerce Court
M5L	Downtown Toronto	Victoria Hotel
M6L	North York	Downsview
M6L	North York	North Park
M6L	North York	Upwood Park
M7L	Not assigned	Not assigned
M8L	Not assigned	Not assigned
M9L	North York	Humber Summit
M1M	Scarborough	Cliffcrest
M1M	Scarborough	Cliffside
M1M	Scarborough	Scarborough Village West
M2M	North York	Newtonbrook
M2M	North York	Willowdale
M3M	North York	Downsview Central
M4M	East Toronto	Studio District
M5M	North York	Bedford Park
M5M	North York	Lawrence Manor East
M6M	York	Del Ray
M6M	York	Keelesdale
M6M	York	Mount Dennis
M6M	York	Silverthorn
M7M	Not assigned	Not assigned
M8M	Not assigned	Not assigned
M9M	North York	Emery
M9M	North York	Humberlea
M1N	Scarborough	Birch Cliff
M1N	Scarborough	Cliffside West
M2N	North York	Willowdale South
M3N	North York	Downsview Northwest
M4N	Central Toronto	Lawrence Park
M5N	Central Toronto	Roselawn
M6N	York	The Junction North
M6N	York	Runnymede
M7N	Not assigned	Not assigned
M8N	Not assigned	Not assigned
M9N	York	Weston
M1P	Scarborough	Dorset Park
M1P	Scarborough	Scarborough Town Centre
M1P	Scarborough	Wexford Heights
M2P	North York	York Mills West
M3P	Not assigned	Not assigned
M4P	Central Toronto	Davisville North
M5P	Central Toronto	Forest Hill North
M5P	Central Toronto	Forest Hill West
M6P	West Toronto	High Park
M6P	West Toronto	The Junction South
M7P	Not assigned	Not assigned
M8P	Not assigned	Not assigned
M9P	Etobicoke	Westmount
M1R	Scarborough	Maryvale
M1R	Scarborough	Wexford
M2R	North York	Willowdale West
M3R	Not assigned	Not assigned
M4R	Central Toronto	North Toronto West
M5R	Central Toronto	The Annex
M5R	Central Toronto	North Midtown
M5R	Central Toronto	Yorkville
M6R	West Toronto	Parkdale
M6R	West Toronto	Roncesvalles
M7R	Mississauga	Canada Post Gateway Processing Centre
M8R	Not assigned	Not assigned
M9R	Etobicoke	Kingsview Village
M9R	Etobicoke	Martin Grove Gardens
M9R	Etobicoke	Richview Gardens
M9R	Etobicoke	St. Phillips
M1S	Scarborough	Agincourt
M2S	Not assigned	Not assigned
M3S	Not assigned	Not assigned
M4S	Central Toronto	Davisville
M5S	Downtown Toronto	Harbord
M5S	Downtown Toronto	University of Toronto
M6S	West Toronto	Runnymede
M6S	West Toronto	Swansea
M7S	Not assigned	Not assigned
M8S	Not assigned	Not assigned
M9S	Not assigned	Not assigned
M1T	Scarborough	Clarks Corners
M1T	Scarborough	Sullivan
M1T	Scarborough	Tam O'Shanter
M2T	Not assigned	Not assigned
M3T	Not assigned	Not assigned
M4T	Central Toronto	Moore Park
M4T	Central Toronto	Summerhill East
M5T	Downtown Toronto	Chinatown
M5T	Downtown Toronto	Grange Park
M5T	Downtown Toronto	Kensington Market
M6T	Not assigned	Not assigned
M7T	Not assigned	Not assigned
M8T	Not assigned	Not assigned
M9T	Not assigned	Not assigned
M1V	Scarborough	Agincourt North
M1V	Scarborough	L'Amoreaux East
M1V	Scarborough	Milliken
M1V	Scarborough	Steeles East
M2V	Not assigned	Not assigned
M3V	Not assigned	Not assigned
M4V	Central Toronto	Deer Park
M4V	Central Toronto	Forest Hill SE
M4V	Central Toronto	Rathnelly
M4V	Central Toronto	South Hill
M4V	Central Toronto	Summerhill West
M5V	Downtown Toronto	CN Tower
M5V	Downtown Toronto	Bathurst Quay
M5V	Downtown Toronto	Island airport
M5V	Downtown Toronto	Harbourfront West
M5V	Downtown Toronto	King and Spadina
M5V	Downtown Toronto	Railway Lands
M5V	Downtown Toronto	South Niagara
M6V	Not assigned	Not assigned
M7V	Not assigned	Not assigned
M8V	Etobicoke	Humber Bay Shores
M8V	Etobicoke	Mimico South
M8V	Etobicoke	New Toronto
M9V	Etobicoke	Albion Gardens
M9V	Etobicoke	Beaumond Heights
M9V	Etobicoke	Humbergate
M9V	Etobicoke	Jamestown
M9V	Etobicoke	Mount Olive
M9V	Etobicoke	Silverstone
M9V	Etobicoke	South Steeles
M9V	Etobicoke	Thistletown
M1W	Scarborough	L'Amoreaux West
M2W	Not assigned	Not assigned
M3W	Not assigned	Not assigned
M4W	Downtown Toronto	Rosedale
M5W	Downtown Toronto	Stn A PO Boxes 25 The Esplanade
M6W	Not assigned	Not assigned
M7W	Not assigned	Not assigned
M8W	Etobicoke	Alderwood
M8W	Etobicoke	Long Branch
M9W	Etobicoke	Northwest
M1X	Scarborough	Upper Rouge
M2X	Not assigned	Not assigned
M3X	Not assigned	Not assigned
M4X	Downtown Toronto	Cabbagetown
M4X	Downtown Toronto	St. James Town
M5X	Downtown Toronto	First Canadian Place
M5X	Downtown Toronto	Underground city
M6X	Not assigned	Not assigned
M7X	Not assigned	Not assigned
M8X	Etobicoke	The Kingsway
M8X	Etobicoke	Montgomery Road
M8X	Etobicoke	Old Mill North
M9X	Not assigned	Not assigned
M1Y	Not assigned	Not assigned
M2Y	Not assigned	Not assigned
M3Y	Not assigned	Not assigned
M4Y	Downtown Toronto	Church and Wellesley
M5Y	Not assigned	Not assigned
M6Y	Not assigned	Not assigned
M7Y	East Toronto	Business Reply Mail Processing Centre 969 Eastern
M8Y	Etobicoke	Humber Bay
M8Y	Etobicoke	King's Mill Park
M8Y	Etobicoke	Kingsway Park South East
M8Y	Etobicoke	Mimico NE
M8Y	Etobicoke	Old Mill South
M8Y	Etobicoke	The Queensway East
M8Y	Etobicoke	Royal York South East
M8Y	Etobicoke	Sunnylea
M9Y	Not assigned	Not assigned
M1Z	Not assigned	Not assigned
M2Z	Not assigned	Not assigned
M3Z	Not assigned	Not assigned
M4Z	Not assigned	Not assigned
M5Z	Not assigned	Not assigned
M6Z	Not assigned	Not assigned
M7Z	Not assigned	Not assigned
M8Z	Etobicoke	Kingsway Park South West
M8Z	Etobicoke	Mimico NW
M8Z	Etobicoke	The Queensway West
M8Z	Etobicoke	Royal York South West
M8Z	Etobicoke	South of Bloor
M9Z	Not assigned	Not assigned
The html table is converted to Pandas DataFrame for cleaning and preprocessing.
In [6]:
dfs = pd.read_html(tab)
df=dfs[0]
df.head()
Out[6]:
Postcode	Borough	Neighbourhood
0	M1A	Not assigned	Not assigned
1	M2A	Not assigned	Not assigned
2	M3A	North York	Parkwoods
3	M4A	North York	Victoria Village
4	M5A	Downtown Toronto	Harbourfront
Data preprocessing and cleaning
In [9]:
# Dropping the rows where Borough is 'Not assigned'
df1 = df[df.Borough != 'Not assigned']

# Combining the neighbourhoods with same Postalcode
df2 = df1.groupby(['Postcode','Borough'], sort=False).agg(', '.join)
df2.reset_index(inplace=True)

# Replacing the name of the neighbourhoods which are 'Not assigned' with names of Borough
df2['Neighbourhood'] = np.where(df2['Neighbourhood'] == 'Not assigned',df2['Borough'], df2['Neighbourhood'])

df2
Out[9]:
Postcode	Borough	Neighbourhood
0	M3A	North York	Parkwoods
1	M4A	North York	Victoria Village
2	M5A	Downtown Toronto	Harbourfront, Regent Park
3	M6A	North York	Lawrence Heights, Lawrence Manor
4	M7A	Queen's Park	Queen's Park
5	M9A	Etobicoke	Islington Avenue
6	M1B	Scarborough	Rouge, Malvern
7	M3B	North York	Don Mills North
8	M4B	East York	Woodbine Gardens, Parkview Hill
9	M5B	Downtown Toronto	Ryerson, Garden District
10	M6B	North York	Glencairn
11	M9B	Etobicoke	Cloverdale, Islington, Martin Grove, Princess ...
12	M1C	Scarborough	Highland Creek, Rouge Hill, Port Union
13	M3C	North York	Flemingdon Park, Don Mills South
14	M4C	East York	Woodbine Heights
15	M5C	Downtown Toronto	St. James Town
16	M6C	York	Humewood-Cedarvale
17	M9C	Etobicoke	Bloordale Gardens, Eringate, Markland Wood, Ol...
18	M1E	Scarborough	Guildwood, Morningside, West Hill
19	M4E	East Toronto	The Beaches
20	M5E	Downtown Toronto	Berczy Park
21	M6E	York	Caledonia-Fairbanks
22	M1G	Scarborough	Woburn
23	M4G	East York	Leaside
24	M5G	Downtown Toronto	Central Bay Street
25	M6G	Downtown Toronto	Christie
26	M1H	Scarborough	Cedarbrae
27	M2H	North York	Hillcrest Village
28	M3H	North York	Bathurst Manor, Downsview North, Wilson Heights
29	M4H	East York	Thorncliffe Park
...	...	...	...
73	M4R	Central Toronto	North Toronto West
74	M5R	Central Toronto	The Annex, North Midtown, Yorkville
75	M6R	West Toronto	Parkdale, Roncesvalles
76	M7R	Mississauga	Canada Post Gateway Processing Centre
77	M9R	Etobicoke	Kingsview Village, Martin Grove Gardens, Richv...
78	M1S	Scarborough	Agincourt
79	M4S	Central Toronto	Davisville
80	M5S	Downtown Toronto	Harbord, University of Toronto
81	M6S	West Toronto	Runnymede, Swansea
82	M1T	Scarborough	Clarks Corners, Sullivan, Tam O'Shanter
83	M4T	Central Toronto	Moore Park, Summerhill East
84	M5T	Downtown Toronto	Chinatown, Grange Park, Kensington Market
85	M1V	Scarborough	Agincourt North, L'Amoreaux East, Milliken, St...
86	M4V	Central Toronto	Deer Park, Forest Hill SE, Rathnelly, South Hi...
87	M5V	Downtown Toronto	CN Tower, Bathurst Quay, Island airport, Harbo...
88	M8V	Etobicoke	Humber Bay Shores, Mimico South, New Toronto
89	M9V	Etobicoke	Albion Gardens, Beaumond Heights, Humbergate, ...
90	M1W	Scarborough	L'Amoreaux West
91	M4W	Downtown Toronto	Rosedale
92	M5W	Downtown Toronto	Stn A PO Boxes 25 The Esplanade
93	M8W	Etobicoke	Alderwood, Long Branch
94	M9W	Etobicoke	Northwest
95	M1X	Scarborough	Upper Rouge
96	M4X	Downtown Toronto	Cabbagetown, St. James Town
97	M5X	Downtown Toronto	First Canadian Place, Underground city
98	M8X	Etobicoke	The Kingsway, Montgomery Road, Old Mill North
99	M4Y	Downtown Toronto	Church and Wellesley
100	M7Y	East Toronto	Business Reply Mail Processing Centre 969 Eastern
101	M8Y	Etobicoke	Humber Bay, King's Mill Park, Kingsway Park So...
102	M8Z	Etobicoke	Kingsway Park South West, Mimico NW, The Queen...
103 rows × 3 columns

In [10]:
# Shape of data frame
df2.shape
Out[10]:
(103, 3)
Importing the csv file conatining the latitudes and longitudes for various neighbourhoods in Canada
In [11]:
lat_lon = pd.read_csv('https://cocl.us/Geospatial_data')
lat_lon.head()
Out[11]:
Postal Code	Latitude	Longitude
0	M1B	43.806686	-79.194353
1	M1C	43.784535	-79.160497
2	M1E	43.763573	-79.188711
3	M1G	43.770992	-79.216917
4	M1H	43.773136	-79.239476
Merging the two tables for getting the Latitudes and Longitudes for various neighbourhoods in Canada
In [12]:
lat_lon.rename(columns={'Postal Code':'Postcode'},inplace=True)
df3 = pd.merge(df2,lat_lon,on='Postcode')
df3.head()
Out[12]:
Postcode	Borough	Neighbourhood	Latitude	Longitude
0	M3A	North York	Parkwoods	43.753259	-79.329656
1	M4A	North York	Victoria Village	43.725882	-79.315572
2	M5A	Downtown Toronto	Harbourfront, Regent Park	43.654260	-79.360636
3	M6A	North York	Lawrence Heights, Lawrence Manor	43.718518	-79.464763
4	M7A	Queen's Park	Queen's Park	43.662301	-79.389494
The notebook from here includes the Clustering and the plotting of the neighbourhoods of Canada which contain Toronto in their Borough
Getting all the rows from the data frame which contains Toronto in their Borough.
In [13]:
df4 = df3[df3['Borough'].str.contains('Toronto',regex=False)]
df4
Out[13]:
Postcode	Borough	Neighbourhood	Latitude	Longitude
2	M5A	Downtown Toronto	Harbourfront, Regent Park	43.654260	-79.360636
9	M5B	Downtown Toronto	Ryerson, Garden District	43.657162	-79.378937
15	M5C	Downtown Toronto	St. James Town	43.651494	-79.375418
19	M4E	East Toronto	The Beaches	43.676357	-79.293031
20	M5E	Downtown Toronto	Berczy Park	43.644771	-79.373306
24	M5G	Downtown Toronto	Central Bay Street	43.657952	-79.387383
25	M6G	Downtown Toronto	Christie	43.669542	-79.422564
30	M5H	Downtown Toronto	Adelaide, King, Richmond	43.650571	-79.384568
31	M6H	West Toronto	Dovercourt Village, Dufferin	43.669005	-79.442259
36	M5J	Downtown Toronto	Harbourfront East, Toronto Islands, Union Station	43.640816	-79.381752
37	M6J	West Toronto	Little Portugal, Trinity	43.647927	-79.419750
41	M4K	East Toronto	The Danforth West, Riverdale	43.679557	-79.352188
42	M5K	Downtown Toronto	Design Exchange, Toronto Dominion Centre	43.647177	-79.381576
43	M6K	West Toronto	Brockton, Exhibition Place, Parkdale Village	43.636847	-79.428191
47	M4L	East Toronto	The Beaches West, India Bazaar	43.668999	-79.315572
48	M5L	Downtown Toronto	Commerce Court, Victoria Hotel	43.648198	-79.379817
54	M4M	East Toronto	Studio District	43.659526	-79.340923
61	M4N	Central Toronto	Lawrence Park	43.728020	-79.388790
62	M5N	Central Toronto	Roselawn	43.711695	-79.416936
67	M4P	Central Toronto	Davisville North	43.712751	-79.390197
68	M5P	Central Toronto	Forest Hill North, Forest Hill West	43.696948	-79.411307
69	M6P	West Toronto	High Park, The Junction South	43.661608	-79.464763
73	M4R	Central Toronto	North Toronto West	43.715383	-79.405678
74	M5R	Central Toronto	The Annex, North Midtown, Yorkville	43.672710	-79.405678
75	M6R	West Toronto	Parkdale, Roncesvalles	43.648960	-79.456325
79	M4S	Central Toronto	Davisville	43.704324	-79.388790
80	M5S	Downtown Toronto	Harbord, University of Toronto	43.662696	-79.400049
81	M6S	West Toronto	Runnymede, Swansea	43.651571	-79.484450
83	M4T	Central Toronto	Moore Park, Summerhill East	43.689574	-79.383160
84	M5T	Downtown Toronto	Chinatown, Grange Park, Kensington Market	43.653206	-79.400049
86	M4V	Central Toronto	Deer Park, Forest Hill SE, Rathnelly, South Hi...	43.686412	-79.400049
87	M5V	Downtown Toronto	CN Tower, Bathurst Quay, Island airport, Harbo...	43.628947	-79.394420
91	M4W	Downtown Toronto	Rosedale	43.679563	-79.377529
92	M5W	Downtown Toronto	Stn A PO Boxes 25 The Esplanade	43.646435	-79.374846
96	M4X	Downtown Toronto	Cabbagetown, St. James Town	43.667967	-79.367675
97	M5X	Downtown Toronto	First Canadian Place, Underground city	43.648429	-79.382280
99	M4Y	Downtown Toronto	Church and Wellesley	43.665860	-79.383160
100	M7Y	East Toronto	Business Reply Mail Processing Centre 969 Eastern	43.662744	-79.321558
Visualizing all the Neighbourhoods of the above data frame using Folium
In [14]:
map_toronto = folium.Map(location=[43.651070,-79.347015],zoom_start=10)

for lat,lng,borough,neighbourhood in zip(df4['Latitude'],df4['Longitude'],df4['Borough'],df4['Neighbourhood']):
    label = '{}, {}'.format(neighbourhood, borough)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
    [lat,lng],
    radius=5,
    popup=label,
    color='blue',
    fill=True,
    fill_color='#3186cc',
    fill_opacity=0.7,
    parse_html=False).add_to(map_toronto)
map_toronto
Out[14]:
The map might not be visible on Github. Check out the README for the map.
Using KMeans clustering for the clsutering of the neighbourhoods
In [24]:
k=5
toronto_clustering = df4.drop(['Postcode','Borough','Neighbourhood'],1)
kmeans = KMeans(n_clusters = k,random_state=0).fit(toronto_clustering)
kmeans.labels_
df4.insert(0, 'Cluster Labels', kmeans.labels_)
Out[24]:
array([0, 0, 0, 3, 0, 0, 2, 0, 4, 0, 2, 3, 0, 2, 3, 0, 3, 1, 1, 1, 1, 4,
       1, 2, 4, 1, 2, 4, 1, 2, 1, 0, 0, 0, 0, 0, 0, 3], dtype=int32)
In [21]:
df4
Out[21]:
Cluster Labels	Postcode	Borough	Neighbourhood	Latitude	Longitude
2	0	M5A	Downtown Toronto	Harbourfront, Regent Park	43.654260	-79.360636
9	0	M5B	Downtown Toronto	Ryerson, Garden District	43.657162	-79.378937
15	0	M5C	Downtown Toronto	St. James Town	43.651494	-79.375418
19	4	M4E	East Toronto	The Beaches	43.676357	-79.293031
20	0	M5E	Downtown Toronto	Berczy Park	43.644771	-79.373306
24	0	M5G	Downtown Toronto	Central Bay Street	43.657952	-79.387383
25	1	M6G	Downtown Toronto	Christie	43.669542	-79.422564
30	0	M5H	Downtown Toronto	Adelaide, King, Richmond	43.650571	-79.384568
31	2	M6H	West Toronto	Dovercourt Village, Dufferin	43.669005	-79.442259
36	0	M5J	Downtown Toronto	Harbourfront East, Toronto Islands, Union Station	43.640816	-79.381752
37	1	M6J	West Toronto	Little Portugal, Trinity	43.647927	-79.419750
41	4	M4K	East Toronto	The Danforth West, Riverdale	43.679557	-79.352188
42	0	M5K	Downtown Toronto	Design Exchange, Toronto Dominion Centre	43.647177	-79.381576
43	1	M6K	West Toronto	Brockton, Exhibition Place, Parkdale Village	43.636847	-79.428191
47	4	M4L	East Toronto	The Beaches West, India Bazaar	43.668999	-79.315572
48	0	M5L	Downtown Toronto	Commerce Court, Victoria Hotel	43.648198	-79.379817
54	4	M4M	East Toronto	Studio District	43.659526	-79.340923
61	3	M4N	Central Toronto	Lawrence Park	43.728020	-79.388790
62	3	M5N	Central Toronto	Roselawn	43.711695	-79.416936
67	3	M4P	Central Toronto	Davisville North	43.712751	-79.390197
68	3	M5P	Central Toronto	Forest Hill North, Forest Hill West	43.696948	-79.411307
69	2	M6P	West Toronto	High Park, The Junction South	43.661608	-79.464763
73	3	M4R	Central Toronto	North Toronto West	43.715383	-79.405678
74	1	M5R	Central Toronto	The Annex, North Midtown, Yorkville	43.672710	-79.405678
75	2	M6R	West Toronto	Parkdale, Roncesvalles	43.648960	-79.456325
79	3	M4S	Central Toronto	Davisville	43.704324	-79.388790
80	1	M5S	Downtown Toronto	Harbord, University of Toronto	43.662696	-79.400049
81	2	M6S	West Toronto	Runnymede, Swansea	43.651571	-79.484450
83	3	M4T	Central Toronto	Moore Park, Summerhill East	43.689574	-79.383160
84	1	M5T	Downtown Toronto	Chinatown, Grange Park, Kensington Market	43.653206	-79.400049
86	3	M4V	Central Toronto	Deer Park, Forest Hill SE, Rathnelly, South Hi...	43.686412	-79.400049
87	0	M5V	Downtown Toronto	CN Tower, Bathurst Quay, Island airport, Harbo...	43.628947	-79.394420
91	0	M4W	Downtown Toronto	Rosedale	43.679563	-79.377529
92	0	M5W	Downtown Toronto	Stn A PO Boxes 25 The Esplanade	43.646435	-79.374846
96	0	M4X	Downtown Toronto	Cabbagetown, St. James Town	43.667967	-79.367675
97	0	M5X	Downtown Toronto	First Canadian Place, Underground city	43.648429	-79.382280
99	0	M4Y	Downtown Toronto	Church and Wellesley	43.665860	-79.383160
100	4	M7Y	East Toronto	Business Reply Mail Processing Centre 969 Eastern	43.662744	-79.321558
In [22]:
# create map
map_clusters = folium.Map(location=[43.651070,-79.347015],zoom_start=10)

# set color scheme for the clusters
x = np.arange(k)
ys = [i + x + (i*x)**2 for i in range(k)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, neighbourhood, cluster in zip(df4['Latitude'], df4['Longitude'], df4['Neighbourhood'], df4['Cluster Labels']):
    label = folium.Popup(' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color=rainbow[cluster-1],
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
Out[22]:
The map might not be visible on Github. Check out the README for the map.
