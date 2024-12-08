# S&P500 earnings yield and 10-year TIPS rate
#### initiated:  2023 08
#### current version:  2024 12 08
### Uses current and historical data
- index of stock prices for the S&P500
- operating and reported earnings for the S&P500
- projections of operating and reported earnings
- interest rate on 10-year TIPS
- operating margins for the S&P500

### Future extension
- composition of earnings by "industry"
- heat maps

### update_data.py
- reads new data from .xlsx workbooks in input_dir
- S&P data downloaded from S&P's weekly posts
- TIPS data downloaded from FRED database
- writes json and parquet files to output_dir
- archives the workbooks from input_dir
### display_data.py
- reads the files in output_dir
- produces pdf documents in display_dir
- presents quarterly data, 2018 through the present
    - page0: projected versus actual earnings
    - page1: future and historical price-earnings ratios
    - page2: margin and equity premium using trailing earnings
    - page3: equity premium usingnprojected earnings
### sources
- https://www.spglobal.com/spdji/en/search/?query=index+earnings&activeTab=all
- https://fred.stlouisfed.org/series/DFII10/chart
### file structure
S&P500-ep-project/
- README.md
- pyproject.toml
- poetry.lock
- .venv
- sp500-ep-project/
    - paths.py
    - update_data.py
    - display_data.py
    - helper_functions/
        - \_\_init__.py
        - helper_func.py
        - read_data_func.py
        - plot_func.py
        - display_helper_func.py
- input_dir/
- output_dir/
    - sp500_pe_df_actuals.parquet
    - estimates/
        - sp-500-eps-est YYYY MM DD.parquet
- display_dir/
    - eps_page0.pdf
    - eps_page1.pdf
    - eps_page2.pdf
    - eps_page3.pdf
- record_dict.json
- backup_dir/
    - backup_pe_df_actuals.parquet
    - backup_record_dict.json
<br>
<br>

## Instructions
### prepare new data if any
1. Put new .xlsx from S&P into input_dir
    - https://www.spglobal.com/spdji/en/search/?query=index+earnings&activeTab=all
    - name: sp-500-eps-est YYYY MM DD.xlsx
    
2. Put new .xlsx from FRED into input_dir
    - https://fred.stlouisfed.org/series/DFII10/chart
    - name: DFII10.xlsx
    - select quarterly, end-of-period observations
    - select max period in FRED
    - download as .xls from FRED
    - add observation for current quarter to end of last row
    - save .xls as .xlsx file into input_dir

### update_data.py
1. set ARCHIVE_DIR in sp500_ep_project/paths.py to your archive
2. run update_data.py
    - reads files in input_dir/
    - moves input files to archive
    - writes the existing .json to backup_dir/
    - writes new .json file to sp500-ep-project/record_dict.json
    - moves sp500_pe_df_actuals.parquet to backup_dir/
    - writes new sp500_pe_df_actuals.parquet
    - writes output files to output_dir/estimates/

### display_data.py
- run display_data.py
    - reads record_dict.json
    - reads files in output_dir/
    - writes .pdf pages to display_dir/
- pdf pages constitute the output
<br>
<br>

## Other Information
### paths.py
-  Contains global variables with addresses for all files
    - addresses of all folders and files fixed by the location of the sp500_ep_project folder
    - user must specify location of ARCHIVE which contains input files after they have been read
    - addresses the project files fixed by the tree shown above for the file structure
- uses Path()

### output_dir/
#### sp-500-eps-est YYYY MM DD.parquet
- polars dataframe with projected earnings
- from sp-500-eps-est YYYY MM DD.xlsx
- uses files with the latest date for each quarter
- creates an output file for each input file

#### sp500_pe_df_actuals.parquet
- one polars dataframe for all historical data
- completely udated from new input data
### record_dict.json
- records all data files read and written
- records which files have been used
- maintains date of latest file read
- maintains list of quarters covered by data
<br>
<br>

#### To recreate/reinitialize entire set of data files from all history
1. paths.py under ARCHIVE_DIR
    - remove # before INPUT_DIR = ARCHIVE_DIR
    - remove # before INPUT_RR_ADDR = ARCHIVE_DIR / INPUT_RR_FILE
2. delete record_dict.json file
3. output_dir/
    - delete sp500_pe_df_actuals.parquet
    - delete all files inside estimates/ subdirectory
4. launch update_data.py
