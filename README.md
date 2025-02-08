# IFs-Dyadic-Trade
Python scripts that process bilateral trade related tables for the International Futures system. For information on data collection across data sources, please refer to our wiki page at [IFs Dyadic Trade Data](https://pardeewiki.du.edu//index.php?title=IFs_Dyadic_Trade_Data). Below is the introduction of the files stored in this repo.


## Concordance Tables
Files under the concordance folder are tables needed for data processing. These include,
 - Country name concordance tables. During each update, it is suggested that users use the scritps to double check if entity names under the Excel file are up to date.
 - GDP deflators. To make trade tables compatible with IFs, we need to convert trade values to IFs unit (currently constant $2017). It is suggested that users download the latest version of GDP deflators from the WDI portal and replace the old files.
 - Mapping tables that aggregate data from 6-digit HS codes to GTAP sectors, and eventually to IFs sectors. Users can find the latest version of HS code mappings from the WITS website.  

## Python Scripts
All data processing scripts are in Python under the Jupyter Notebook,
  - Trade_Service_UNComtrade.ipynb processes service trade data from UN Comtrade. Since IFs do not breakdown service sector into smaller subcategories at the moment, plus there is no tariff information on service trade, the processing of service trade data is different than trade and tariff data from BACI and WITS. This script takes the raw data from UN Comtrade and produces IFs formatted bilater trade data in the service sector.
  - TCI_Goods_BACI.ipynb processes trade data from BACI, for two purposes.
    - It formats the trade data by converting entities to IFs entities, adding each commodity's associated IFs sector to the tables, and saving them as CSV files for later calculation of tariff revenues and weighter tariffs.
    - It calculates the TCI index at sectoral as well as overall levels. Note that calculating TCI requires extensively usage of RAM and a relatively long computing time (approxiamtely 1.5hrs for one year of data). Resultant TCI tables are saved as CSV files for further formatting.
  - Trade_Goods_BACI.ipynb has detailed notes on data from BACI and WITS. It merges trade data from BACI and tariff data from WITS to calculate tariff revenues and weighted tariffs. **Note that users need to run the above script first (TCI_Goods_BACI.ipynb) to get formatted trade data**. Outputs are saved by year under CSV files.
  - Output to IFs.ipynb takes all the output files from the three scripts above, and inserts them all into a SQLite database.

## Notes
1. Users need to create an output folder with corresponding sub-directories to store all the intermediate outputs.
2. Users need to re-define the path to the SQLite database if desired final output is a SQLite database file. However, users can also ignore the last block of Output to IFs.ipynb if desired outputs are simply formatted IFs bilateral data stored in CSV files. 

## Improvement & Customization
1. The calculation of TCI relies heavily on pandas dataframe's vectorization. However, it still goes through each country-pairs as well as each year of the data (a nested-loop). If capacity allows, users can multi-process the calculation. Or if users have a better algorithm to calcualte the TCI, feel free to contribute!
2. This script only produces TCI at IFs sectoral level. However, users can easily get TCI at any other sub-categories or customized commodity groups by modifying or creating a mapping table based on 6-digit HS code or GTAP code.
3. When processing trade data and tariff data, the scripts currently rely on the HS92 coding system, which might lead to some mismatching situations. For example, certain commodities may have been broken down into smaller cateories throughout the years and assigned with different tariff rates. 
4. Some traded commodities have no reported tariff rates for some country-pairs. Currently, the script imputes these missing values using reporter-commodity level averages, and then global-commodity level averages if the former one does not exist. Users can pick other strategies in terms tariff rates imputation. One possibility is to look for average duty rates for the commodities of the same category using 4-digit HS code (or 2-digit HS code, or GTAP code). 


# Contributing
We welcome contributions and feedback. Here's how you can help:
- [Report issues](<https://github.com/PardeeCenterDU/IFs-Dyadic-Trade/issues>) 
- [Contact us](mailto:yutang.xiong@du.edu)
