# Dependencies
import pandas as pd

# Save path to data set in a variable
data_file_path = "purchase_data.json" #also works with file purchase_data2.json

# Read the modified json file and store into Pandas DataFrame
data_file_df = pd.read_json(data_file_path, encoding="utf-8")
data_file_df.head(10)

# Calculate the number of total players in the DataFrame
total_players = len(data_file_df["SN"].unique())

# Player Count Summary
# Place the total number of players into a DataFrame

playercount_table = pd.DataFrame({'Total Players':[total_players]})                           
                             
playercount_table

# Calculate number of unique items
unique_items = len(data_file_df["Item ID"].unique())

# Calculate average purchase price
average_price = round(data_file_df["Price"].mean(), 2)

# Calculate total number of purchases 
total_purchases = len(data_file_df["Item ID"])

# Calculate total revenue
total_revenue = sum(data_file_df["Price"])

# Purchasing Analysis (Total) Summary
# Place all of the data found into a summary DataFrame
summary_table = pd.DataFrame({"Number of Unique Items":unique_items,
                             "Average Purchase Price":[average_price],
                             "Total Number of Purchases":[total_purchases],
                             "Total Revenue":[total_revenue]})
summary_table

# Percentage and Count of Male Players
male_players = len(data_file_df.loc[data_file_df.Gender == "Male"]["SN"].unique())
percentage_male = round(male_players/total_players, 4)*100

# Percentage and Count of Female Players
female_players = len(data_file_df.loc[data_file_df.Gender == "Female"]["SN"].unique())
percentage_female = round(female_players/total_players, 4)*100

# Percentage and Count of Other / Non-Disclosed
other_players = len(data_file_df.loc[data_file_df.Gender == "Other / Non-Disclosed"]["SN"].unique())
percentage_other = round(other_players/total_players, 4)*100

# Gender Demographics Summary
# Place all of the data found into a summary DataFrame
gender_demographics = pd.DataFrame({"Percentage of Players":[percentage_male, percentage_female, percentage_other],
                             "Total Count":[male_players, female_players,other_players],
                             }, index=["Male","Female","Other / NonDisclosed" ])

gender_demographics

# Male purchase count, average purchase price, total purchase value, and normalized totals 
male_purchase_count = len(data_file_df.loc[data_file_df.Gender == "Male"])
male_total_purchase_value = round(data_file_df.loc[data_file_df.Gender == "Male"]["Price"].sum(),2)
male_average_purchase_value = round(male_total_purchase_value/male_purchase_count,2)
male_normalized_total = round(male_total_purchase_value/male_players,2)

# Female purchase count, average purchase price, total purchase value, and normalized totals 
female_purchase_count = len(data_file_df.loc[data_file_df.Gender == "Female"])
female_total_purchase_value = round(data_file_df.loc[data_file_df.Gender == "Female"]["Price"].sum(),2)
female_average_purchase_value = round(female_total_purchase_value/female_purchase_count,2)
female_normalized_total = round(female_total_purchase_value/female_players,2)

# Other / non-disclosed purchase count, average purchase price, total purchase value, and normalized totals 
other_purchase_count = len(data_file_df.loc[data_file_df.Gender == "Other / Non-Disclosed"])
other_total_purchase_value = round(data_file_df.loc[data_file_df.Gender == "Other / Non-Disclosed"]["Price"].sum(),2)
other_average_purchase_value = round(other_total_purchase_value/other_purchase_count,2)
other_normalized_total = round(other_total_purchase_value/other_players,2)

# Purchasing Analysis (Gender) Summary
# Place all of the data found into a summary DataFrame
purchasing_analysis_gender = pd.DataFrame({"Purchase Count":[male_purchase_count, female_purchase_count, other_purchase_count],
                             "Average Purchase Price":[male_average_purchase_value, female_average_purchase_value, other_average_purchase_value], 
                             "Total Purchase Value":[male_total_purchase_value, female_total_purchase_value, other_total_purchase_value],
                             "Normalized Totals":[male_normalized_total, female_normalized_total, other_normalized_total]     
                             }, index=["Male","Female","Other / NonDisclosed" ])

purchasing_analysis_gender

# Create bins in which to place values 
bins = [0, 10, 14, 19, 24, 29, 34, 39, 100]

# Create labels for these bins
group_labels = ["<10", "10 to 14", "15 to 19",
                "20 to 24", "25 to 29", "30 to 34",
                "35 to 39", "40+"]

# Slice the data and place it into bins
pd.cut(data_file_df["Age"],bins,labels=group_labels)

data_file_df["Age Range"] = pd.cut(data_file_df["Age"],
                                           bins, labels=group_labels)
data_file_df.head()

# Age Demographics Summary
# Creating a group based off of the bins
age_groups = data_file_df.groupby("Age Range")
purchase_age_a = pd.DataFrame()
purchase_age_a["Purchase Count"] = age_groups["Item ID"].count()
purchase_age_a["Average Purchase Price"] = round(age_groups["Price"].sum()/age_groups["Item ID"].count(),2)
purchase_age_a["Total Purchase Price"] = round(age_groups["Price"].sum(),2)
purchase_age_a["Normalized Price"] = round(age_groups["Price"].sum()/age_groups["SN"].nunique(),2)
purchase_age_a

#Top Spenders Summary
sn_group = data_file_df.groupby("SN")
purchase_sn_a = pd.DataFrame()
purchase_sn_a["Purchase Count"] = sn_group["Item ID"].count()
purchase_sn_a["Average Purchase Price"] = round(sn_group["Price"].sum()/sn_group["Item ID"].count(),2)
purchase_sn_a["Total Purchase Price"] = round(sn_group["Price"].sum(),2)
purchase_sn_a.sort_values(by="Total Purchase Price", ascending=False).head()

#Most Popular Items Summary
item_group = data_file_df.groupby(["Item ID","Item Name"])
purchase_item_a = pd.DataFrame()
purchase_item_a["Purchase Count"] = item_group["SN"].count()
purchase_item_a["Item Price"] = round(item_group["Price"].sum()/item_group["Item ID"].count(),2)
purchase_item_a["Total Purchase Price"] = round(item_group["Price"].sum(),2)
purchase_item_a.sort_values(by="Purchase Count", ascending=False).head()

#Most Profitable Items Summary
purchase_item_a.sort_values(by="Total Purchase Price", ascending=False).head()