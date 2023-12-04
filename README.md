# Набор нестандартных графиков в Python из собственных проектов

## Половозрастная пирамида с категориями (Matplotlib)
Dataset: https://www.kaggle.com/datasets/utkarshx27/suicide-attempts-in-shandong-china
```Python
import pandas as pd  
import matplotlib.pyplot as plt  
  
df = pd.read_csv("suicide_china.csv")  ###DATA###  
  
df["Age_Intervals"] = pd.cut(df["Age"],  ###DATA###  
                             bins=[0, 4, 9, 14, 19, 24, 29, 34, 39, 44, 49,  
                                   54, 59, 64, 69, 74, 79, 84, 89, 94, 99, 104],  
                             labels=["0-4", "5-9", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39",  
                                     "40-44", "45-49", "50-54", "55-59", "60-64", "65-69", "70-74", "75-79",  
                                     "80-84", "85-89", "90-94", "95-99", "100-104"])  
  
crosstab = pd.crosstab(index=df["Age_Intervals"], columns=[df["Sex"], df["Died"]], dropna=False, normalize="all")  ###DATA###    
  
male_died = [number * 100 for number in crosstab["male"]["yes"]]  ###DATA###  
female_died = [number * 100 for number in crosstab["female"]["yes"]]  ###DATA###  
male_lived = [number * 100 for number in crosstab["male"]["no"]]  ###DATA###  
female_lived = [number * 100 for number in crosstab["female"]["no"]]  ###DATA###  
  
age = ["0-4", "5-9", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40-44", "45-49", "50-54", "55-59", "60-64",  
       "65-69", "70-74", "75-79", "80-84", "85-89", "90-94", "95-99", "100-104"]  
  
pyramid_df = pd.DataFrame({"Age": age, "Male_l": male_lived, "Male_d": male_died,  ###DATA###  
                           "Female_d": female_died, "Female_l": female_lived})  ###DATA###  
  
pyramid_df["Female_Width"] = pyramid_df["Female_d"] + pyramid_df["Female_l"]  
pyramid_df["Male_Width"] = pyramid_df["Male_d"] + pyramid_df["Male_l"]  
pyramid_df["Male_d_Left"] = -pyramid_df["Male_d"]  
pyramid_df["Male_l_Left"] = -pyramid_df["Male_Width"]  
  
fig = plt.figure(figsize=(15, 10))  
  
plt.barh(y=pyramid_df["Age"], width=pyramid_df["Female_d"],  
         color="tab:red", label="Females Died", edgecolor="black")  
plt.barh(y=pyramid_df["Age"], width=pyramid_df["Female_l"], left=pyramid_df["Female_d"],  
         color="tab:orange", label="Females Survived", edgecolor="black")  
plt.barh(y=pyramid_df["Age"], width=pyramid_df["Male_d"], left=pyramid_df["Male_d_Left"],  
         color="tab:blue", label="Males Died", edgecolor="black")  
plt.barh(y=pyramid_df["Age"], width=pyramid_df["Male_l"], left=pyramid_df["Male_l_Left"],  
         color="tab:cyan", label="Males Survived", edgecolor="black")  
  
pyramid_df["Male_d_Text"] = pyramid_df["Male_d_Left"] / 2  
pyramid_df["Male_l_Text"] = (pyramid_df["Male_l_Left"] + pyramid_df["Male_d_Left"]) / 2  
pyramid_df["Female_d_Text"] = (pyramid_df["Female_Width"] + pyramid_df["Female_d"]) / 2  
pyramid_df["Female_l_Text"] = pyramid_df["Female_d"] / 2  
  
for idx in range(len(pyramid_df)):  
    plt.text(x=pyramid_df["Male_d_Text"][idx], y=idx,  
             s="{}%".format(round(pyramid_df["Male_d"][idx], 1)),  
             fontsize=12, ha="center", va="center")  
    plt.text(x=pyramid_df["Male_l_Text"][idx], y=idx,  
             s="{}%".format(round(pyramid_df["Male_l"][idx], 1)),  
             fontsize=12, ha="center", va="center")  
    plt.text(x=pyramid_df["Female_d_Text"][idx], y=idx,  
             s="{}%".format(round(pyramid_df["Female_l"][idx],1)),  
             fontsize=12, ha="center", va="center")  
    plt.text(x=pyramid_df["Female_l_Text"][idx], y=idx,  
             s="{}%".format(round(pyramid_df["Female_d"][idx], 1)),  
             fontsize=12, ha="center", va="center")  
  
plt.xlim(-6, 6)  ###OUT#####  
plt.xticks(range(-6, 7), ["{}%".format(i) for i in range(-6, 7)])  ###OUT###  
  
plt.title("Suicide attempts in Shandong (2009-2011)", pad=20, fontsize=25, fontweight="bold")  ###OUT###  
plt.xlabel("Percentage of suicide attempts", fontsize=20, fontweight="bold")  ###OUT###  
plt.ylabel("Age", fontsize=20, fontweight="bold")  
  
plt.grid(True, alpha=0.4)  
plt.legend(fontsize=20, shadow=True)  
plt.tight_layout()  
plt.savefig("Pyramid_leveled.png")  ###OUT###  
plt.show()
```
![image](https://github.com/Makar-Data/python_plots/assets/152608115/fae731a0-2a9b-4b2b-a6e9-504d9f81c49c)

---

## Половозрастная пирамида с категориями (Plotly)
Dataset: https://www.kaggle.com/datasets/utkarshx27/suicide-attempts-in-shandong-china
```Python
import numpy as np
import pandas as pd
import plotly.graph_objs as go
from plotly.offline import plot

df = pd.read_csv("suicide_china.csv")

df["Age_Intervals"] = pd.cut(df["Age"],  ###DATA###
                             bins=[0, 4, 9, 14, 19, 24, 29, 34, 39, 44, 49,
                                   54, 59, 64, 69, 74, 79, 84, 89, 94, 99, 104],
                             labels=["0-4", "5-9", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39",
                                     "40-44", "45-49", "50-54", "55-59", "60-64", "65-69", "70-74", "75-79",
                                     "80-84", "85-89", "90-94", "95-99", "100-104"])

crosstab = pd.crosstab(index=df["Age_Intervals"], columns=[df["Sex"], df["Died"]], dropna=False)

md = np.array([-number for number in crosstab["male"]["yes"]])
fd = np.array([number for number in crosstab["female"]["yes"]])
ml = np.array([-number for number in crosstab["male"]["no"]])
fl = np.array([number for number in crosstab["female"]["no"]])
y = [interval for interval in crosstab.index]

layout = go.Layout(yaxis=go.layout.YAxis(title="Age"),
                   xaxis=go.layout.XAxis(
                       range=[-150, 150],
                       tickvals=[-150, -125, -100, -75, -50, -25, 0, 25, 50, 75, 100, 125, 150],
                       ticktext=[150, 125, 100, 75, 50, 25, 0, 25, 50, 75, 100, 125, 150],
                       title="Amount"),
                   barmode="relative",
                   bargap=0.1)

data = [go.Bar(y=y,
               x=md,
               orientation="h",
               name="Males Died",
               text=-1 * md.astype('int'),
               hovertemplate="Ages: %{y}, %{text} deaths",
               opacity=1,
               marker=dict(color="#01056b"),
               legendrank=1
               ),
        go.Bar(y=y,
               x=ml,
               orientation="h",
               name="Males Survived",
               hovertemplate="Ages: %{y}, %{text} attemts",
               text=-1 * ml.astype('int'),
               opacity=1,
               marker=dict(color="#1b4a97"),
               legendrank=2
               ),
        go.Bar(y=y,
               x=fd,
               orientation="h",
               name="Females Died",
               hovertemplate="Ages: %{y}, %{text} deaths",
               text=fd.astype('int'),
               opacity=1,
               marker=dict(color="#7d0216"),
               legendrank=3
               ),
        go.Bar(y=y,
               x=fl,
               orientation="h",
               name="Females Survived",
               hovertemplate="Ages: %{y}, %{text} attempts",
               text=fl.astype('int'),
               opacity=1,
               marker=dict(color="#dc0018"),
               legendrank=4
               )]

plot(dict(data=data, layout=layout))
```
![image](https://github.com/Makar-Data/python_plots/assets/152608115/3256196f-50f3-438a-a1c8-dd012f2bc8d8)

---

## Дискретное распределение мнений
Dataset: https://www.kaggle.com/datasets/osmi/mental-health-in-tech-2016
```Python
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

df = pd.read_csv("mental.csv")  ###DATA###
category_names = ["No", "Maybe", "Yes"]  ###DATA### То же, что в csv
needed = df.iloc[:, [10, 11]]  ###DATA###

questions = list(needed.columns.values)
raws = []

list_obj_cols = needed.columns[needed.dtypes == "object"].tolist()
for obj_col in list_obj_cols:
    needed[obj_col] = needed[obj_col].astype(pd.api.types.CategoricalDtype(categories=category_names))

list_cat_cols = needed.columns[needed.dtypes == "category"].tolist()
for cat_col in list_cat_cols:
    dc = needed[cat_col].value_counts().sort_index().reset_index().to_dict(orient="list")
    raws.append(dc["count"])
    print("Question surveyed:", dc)
print("Qustions:", questions)

results = [[num / sum(brackets) * 100 for num in brackets] for brackets in raws]
number_results = {questions[i]: raws[i] for i in range(len(questions))}
percentage_results = {questions[i]: results[i] for i in range(len(questions))}

### Ручная замена labels ###
percentage_results["Ment. disc. negative conseq."] = \
    percentage_results["Do you think that discussing a mental health disorder with your employer would have negative consequences?"]
percentage_results["Phys. disc. negative conseq."] = \
    percentage_results["Do you think that discussing a physical health issue with your employer would have negative consequences?"]

del percentage_results["Do you think that discussing a mental health disorder with your employer would have negative consequences?"]
del percentage_results["Do you think that discussing a physical health issue with your employer would have negative consequences?"]

print("")
print("Graph input:", percentage_results)


def survey(number_results, percentage_results, category_names):
    labels = list(percentage_results.keys())
    data = np.array(list(percentage_results.values()))
    data_cum = data.cumsum(axis=1)
    category_colors = plt.get_cmap("RdYlGn")(
        np.linspace(0.15, 0.85, data.shape[1]))

    fig, ax = plt.subplots(figsize=(9.2, 5))
    ax.invert_yaxis()
    ax.xaxis.set_visible(False)
    ax.set_xlim(0, np.sum(data, axis=1).max())

    for i, (colname, color) in enumerate(zip(category_names, category_colors)):
        widths = data[:, i]
        starts = data_cum[:, i] - widths
        ax.barh(labels, widths, left=starts, height=0.5,
                label=colname, color=color)

        xcenters = starts + widths / 2
        numbers = np.array(list(number_results.values()))[:, i]

        r, g, b, _ = color
        text_color = "white" if r * g * b < 0.5 else "darkgrey"
        for y, (x, c) in enumerate(zip(xcenters, numbers)):
            ax.text(x, y, str(int(c)),
                    ha="center", va="center", color=text_color)
    ax.legend(ncol=len(category_names), bbox_to_anchor=(0, 1),
              loc="lower left", fontsize="small")
    return fig, ax


survey(number_results, percentage_results, category_names)
plt.savefig("Discrete.png")
plt.tight_layout()
plt.show()
```
![image](https://github.com/Makar-Data/python_plots/assets/152608115/13b242e0-86b0-4ce4-8694-e66c225f7031)

