# Global-State-Provinance-Demographic-analyzer
import pandas as pd
import matplotlib.pyplot as plt

data = {
    "State": ["Maharashtra", "Gujarat", "Karnataka",
              "Rajasthan", "Kerala"],
    "Population_M": [125, 70, 68, 82, 35],
    "Literacy": [89, 82, 78, 75, 94],
    "Employment": [62, 58, 60, 55, 64],
    "Income_k": [320, 290, 310, 240, 340]
}

df = pd.DataFrame(data)

# Clean and validate data
metrics = ["Population_M", "Literacy",
           "Employment", "Income_k"]

df[metrics] = df[metrics].apply(
    pd.to_numeric, errors="coerce"
)

df = df.dropna(subset=["State"] + metrics)

df = df[
    df["Literacy"].between(0, 100)
    & df["Employment"].between(0, 100)
    & (df["Population_M"] > 0)
    & (df["Income_k"] >= 0)
]

if df.empty:
    raise ValueError("No valid demographic records found.")

# Statistical summary
print(df[metrics].describe().round(2))

# Rank top 3 states by literacy
print(df.nlargest(3, "Literacy")[
    ["State", "Literacy"]
])

# Rank top 3 states by income
print(df.nlargest(3, "Income_k")[
    ["State", "Income_k"]
])

# Visualize literacy
df.sort_values("Literacy").plot(
    x="State",
    y="Literacy",
    kind="bar",
    legend=False,
    title="Literacy Rate by State"
)
plt.tight_layout()
plt.show()

# Visualize employment
df.plot(
    x="State",
    y="Employment",
    kind="bar",
    legend=False,
    title="Employment Rate by State"
)
plt.tight_layout()
plt.show()

# Compare literacy and income
df.plot.scatter(
    x="Literacy",
    y="Income_k",
    title="Literacy vs Average Income"
)
plt.tight_layout()
plt.show()
