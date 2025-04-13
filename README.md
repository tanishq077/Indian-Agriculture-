# Indian-Agriculture-import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset
df = pd.read_csv(r"C:\Users\ishus\Desktop\pythonca-2\ICRISAT-District Level Data.csv")

# Fill missing values with 0 to avoid plot issues
df.fillna(0, inplace=True)

# List of crops
crops = ['RICE', 'WHEAT', 'MAIZE', 'SORGHUM', 'PEARL MILLET',
         'CHICKPEA', 'PIGEONPEA', 'GROUNDNUT', 'COTTON']
print(df.head())
print(df.describe())

# 1. Yield trends over time
yield_columns = [f"{crop} YIELD (Kg per ha)" for crop in crops if f"{crop} YIELD (Kg per ha)" in df.columns]

# Group by year and calculate average yield
yearly_yield = df.groupby("Year")[yield_columns].mean()

# Plot yield trends for all crops
plt.figure(figsize=(12, 6))
plt.plot(yearly_yield.index, yearly_yield["RICE YIELD (Kg per ha)"], label="RICE")
plt.plot(yearly_yield.index, yearly_yield["WHEAT YIELD (Kg per ha)"], label="WHEAT")
plt.plot(yearly_yield.index, yearly_yield["MAIZE YIELD (Kg per ha)"], label="MAIZE")
plt.plot(yearly_yield.index, yearly_yield["SORGHUM YIELD (Kg per ha)"], label="SORGHUM")
plt.plot(yearly_yield.index, yearly_yield["PEARL MILLET YIELD (Kg per ha)"], label="PEARL MILLET")
plt.title("Crop Yield Trends Over Time")
plt.xlabel("Year")
plt.ylabel("Yield (Kg per ha)")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()

# ------------------------------
# 2. State-wise average yield
# ------------------------------
statewise_yield = df.groupby("State Name")[yield_columns].mean()
top_states = statewise_yield.sort_values(by="RICE YIELD (Kg per ha)", ascending=False).head(10)

plt.figure(figsize=(12, 6))
sns.heatmap(top_states.T, annot=True, cmap="YlGnBu", fmt=".0f")
plt.title("Top 10 States by Rice Yield and Other Crop Yields")
plt.tight_layout()
plt.show()

# ------------------------------
# 3. Area under cultivation
# ------------------------------
area_columns = [f"{crop} AREA (1000 ha)" for crop in crops if f"{crop} AREA (1000 ha)" in df.columns]
yearly_area = df.groupby("Year")[area_columns].sum()

# Plot stacked area chart
plt.figure(figsize=(12, 6))
plt.stackplot(yearly_area.index,
              yearly_area["RICE AREA (1000 ha)"],
              yearly_area["WHEAT AREA (1000 ha)"],
              yearly_area["MAIZE AREA (1000 ha)"],
              yearly_area["SORGHUM AREA (1000 ha)"],
              yearly_area["PEARL MILLET AREA (1000 ha)"],
              labels=["RICE", "WHEAT", "MAIZE", "SORGHUM", "PEARL MILLET"],
              alpha=0.8)
plt.title("Total Area Under Cultivation Over Years")
plt.xlabel("Year")
plt.ylabel("Area (1000 ha)")
plt.legend(loc="upper left")
plt.tight_layout()
plt.show()

# ------------------------------
# 4. Correlation (for RICE)
# ------------------------------
# Define RICE-specific columns
rice_area = "RICE AREA (1000 ha)"
rice_yield = "RICE YIELD (Kg per ha)"
rice_prod = "RICE PRODUCTION (1000 tons)"

# Get relevant columns for RICE
df_rice = df[[rice_area, rice_yield, rice_prod]]

# Plot Area vs Yield and Production vs Yield
plt.figure(figsize=(12, 5))

plt.subplot(1, 2, 1)
sns.scatterplot(x=rice_area, y=rice_yield, data=df_rice)
plt.title("RICE: Area vs Yield")

plt.subplot(1, 2, 2)
sns.scatterplot(x=rice_prod, y=rice_yield, data=df_rice)
plt.title("RICE: Production vs Yield")

plt.tight_layout()
plt.show()

# Correlation matrix
plt.figure(figsize=(4, 4))
sns.heatmap(df_rice.corr(), annot=True, cmap="coolwarm")
plt.title("RICE: Correlation Matrix")
plt.tight_layout()
plt.show()

# ------------------------------
# 5. Top districts for RICE
# ------------------------------
# Find top 5 districts for RICE area
rice_districts = df.groupby(["State Name", "Dist Name"])[rice_area].sum().sort_values(ascending=False).head(5).reset_index()

# Plot as bar chart
plt.figure(figsize=(10, 5))
sns.barplot(x="Dist Name", y=rice_area, data=rice_districts)
plt.title("Top 5 Districts with Highest RICE Area")
plt.xlabel("District")
plt.ylabel("Area (1000 ha)")
plt.tight_layout()
plt.show()


# Set up Streamlit
st.set_page_config(layout="wide")

# Load the dataset using file uploader
uploaded_file = st.file_uploader("Upload the CSV file", type="csv")
if uploaded_file is not None:
    df = pd.read_csv(uploaded_file)

    # Fill missing values with 0 to avoid plot issues
    df.fillna(0, inplace=True)

    # List of crops
    crops = ['RICE', 'WHEAT', 'MAIZE', 'SORGHUM', 'PEARL MILLET',
             'CHICKPEA', 'PIGEONPEA', 'GROUNDNUT', 'COTTON']

    # ------------------------------
    # 1. Yield trends over time
    # ------------------------------
    # Get yield columns for available crops
    yield_columns = [f"{crop} YIELD (Kg per ha)" for crop in crops if f"{crop} YIELD (Kg per ha)" in df.columns]

    # Group by year and calculate average yield
    yearly_yield = df.groupby("Year")[yield_columns].mean()

    # Plot yield trends for all crops
    plt.figure(figsize=(12, 6))
    for crop in crops:
        if f"{crop} YIELD (Kg per ha)" in yearly_yield.columns:
            plt.plot(yearly_yield.index, yearly_yield[f"{crop} YIELD (Kg per ha)"], label=crop)

    plt.title("Crop Yield Trends Over Time")
    plt.xlabel("Year")
    plt.ylabel("Yield (Kg per ha)")
    plt.legend()
    plt.grid(True)
    plt.tight_layout()
    st.pyplot(plt)

    # ------------------------------
    # 2. State-wise average yield
    # ------------------------------
    statewise_yield = df.groupby("State Name")[yield_columns].mean()
    top_states = statewise_yield.sort_values(by="RICE YIELD (Kg per ha)", ascending=False).head(10)

    plt.figure(figsize=(12, 6))
    sns.heatmap(top_states.T, annot=True, cmap="YlGnBu", fmt=".0f")
    plt.title("Top 10 States by Rice Yield and Other Crop Yields")
    plt.tight_layout()
    st.pyplot(plt)

    # ------------------------------
    # 3. Area under cultivation
    # ------------------------------
    area_columns = [f"{crop} AREA (1000 ha)" for crop in crops if f"{crop} AREA (1000 ha)" in df.columns]
    yearly_area = df.groupby("Year")[area_columns].sum()

    # Plot stacked area chart
    plt.figure(figsize=(12, 6))
    plt.stackplot(yearly_area.index,
                  *[yearly_area[col] for col in yearly_area.columns],
                  labels=[col.split()[0] for col in yearly_area.columns],
                  alpha=0.8)
    plt.title("Total Area Under Cultivation Over Years")
    plt.xlabel("Year")
    plt.ylabel("Area (1000 ha)")
    plt.legend(loc="upper left")
    plt.tight_layout()
    st.pyplot(plt)

    # ------------------------------
    # 4. Correlation (for RICE)
    # ------------------------------
    # Define RICE-specific columns
    rice_area = "RICE AREA (1000 ha)"
    rice_yield = "RICE YIELD (Kg per ha)"
    rice_prod = "RICE PRODUCTION (1000 tons)"

    # Get relevant columns for RICE
    df_rice = df[[rice_area, rice_yield, rice_prod]]

    # Plot Area vs Yield and Production vs Yield
    plt.figure(figsize=(12, 5))

    plt.subplot(1, 2, 1)
    sns.scatterplot(x=rice_area, y=rice_yield, data=df_rice)
    plt.title("RICE: Area vs Yield")

    plt.subplot(1, 2, 2)
    sns.scatterplot(x=rice_prod, y=rice_yield, data=df_rice)
    plt.title("RICE: Production vs Yield")

    plt.tight_layout()
    st.pyplot(plt)

    # Correlation matrix
    plt.figure(figsize=(4, 4))
    sns.heatmap(df_rice.corr(), annot=True, cmap="coolwarm")
    plt.title("RICE: Correlation Matrix")
    plt.tight_layout()
    st.pyplot(plt)

    # ------------------------------
    # 5. Top districts for RICE
    # ------------------------------
    # Find top 5 districts for RICE area
    rice_districts = df.groupby(["State Name", "Dist Name"])[rice_area].sum().sort_values(ascending=False).head(5).reset_index()

    # Plot as bar chart
    plt.figure(figsize=(10, 5))
    sns.barplot(x="Dist Name", y=rice_area, data=rice_districts)
    plt.title("Top 5 Districts with Highest RICE Area")
    plt.xlabel("District")
    plt.ylabel("Area (1000 ha)")
    plt.tight_layout()
    st.pyplot(plt)
