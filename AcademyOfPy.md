

```python
# import modules
import pandas as pd
import os
```


```python
# assign file path
file_path1 = os.path.join('PyCitySchools\\raw_data','schools_complete.csv')
file_path2 = os.path.join('PyCitySchools\\raw_data','students_complete.csv')
# read the files
sch_df = pd.read_csv(file_path1)
stu_df = pd.read_csv(file_path2)
stu_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Student ID</th>
      <th>name</th>
      <th>gender</th>
      <th>grade</th>
      <th>school</th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Victor Smith</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>94</td>
      <td>61</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Kevin Rodriguez</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>90</td>
      <td>60</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Dr. Richard Scott</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>67</td>
      <td>58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Bonnie Ray</td>
      <td>F</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>97</td>
      <td>84</td>
    </tr>
  </tbody>
</table>
</div>




```python
# District Summary
# No. of Schools
school_count = sch_df["School ID"].nunique()
# No. of Students
student_count = len(set(stu_df["Student ID"]))
# District Budget
total_budget = sch_df["budget"].sum()
# Average Mat Score
# avg_math_score = stu_df["math_score"].sum()/stu_df["math_score"].count()
avg_math_score = round(stu_df["math_score"].mean(),2)
# Average Reading Score
avg_reading_score = stu_df["reading_score"].mean()
# No. of students who passed Math and Reading
math_pass = stu_df[stu_df["math_score"] >= 70]
read_pass = stu_df[stu_df["reading_score"] >= 70]
# Math and Reading Pass Percentage
math_pass_percent = (math_pass["math_score"].count()/stu_df["math_score"].count()) * 100
read_pass_percent = (read_pass["reading_score"].count()/stu_df["reading_score"].count()) * 100

# create a DataFrame 
district_summary = pd.DataFrame({"Total Schools": school_count,
                         "Total Students": student_count,
                         "Total Budget": total_budget,
                         "Average Math Score": avg_math_score,
                         "Average Reading Score": avg_reading_score,
                         "% Passing Math" : math_pass_percent,
                         "% Passing Reading": read_pass_percent,
                         "% Overall Passing Rate": ((math_pass_percent+ read_pass_percent)/2)}, index=[0])

district_summary = district_summary[["Total Schools","Total Students","Total Budget","Average Math Score","Average Reading Score",
                "% Passing Math","% Passing Reading","% Overall Passing Rate"]]
district_summary.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>74.98</td>
      <td>85.81</td>
      <td>80.39</td>
    </tr>
  </tbody>
</table>
</div>




```python
# School Summary
# Merge School and Student DataFrame
school_data = sch_df.rename(columns={"name": "School Name",
                                     "type": "School Type"})
student_data = stu_df.rename(columns={"name": "Student Name",
                                     "school": "School Name"})

consol_df = pd.merge(school_data, student_data)
consol_df.fillna(0,inplace=True) # fil NaN with 0
```


```python
# Group the Consolidated DataFrame based on School
school_group = consol_df.groupby(["School Name"])
# Calculate No. of students in each school
student_count = school_group["Student Name"].count()
# Set school budget
school_budget = school_group["budget"].unique()
# Per Student Budget
student_budget = school_budget/student_count
# Avg. Math and Reading score of each school
avg_math_score = school_group["math_score"].mean()
avg_read_score = school_group["reading_score"].mean()
# No. of students who passed Math and Reading
math_pass = consol_df[consol_df["math_score"] >= 70]
read_pass = consol_df[consol_df["reading_score"] >= 70]
# Math and Reading Pass Percentage
math_pass_percent = (math_pass.groupby(["School Name"])["math_score"].count()/school_group["math_score"].count()) * 100
read_pass_percent = (read_pass.groupby(["School Name"])["reading_score"].count()/school_group["reading_score"].count()) * 100

# Create a DataFrame for School Summary
school_summary = pd.DataFrame({ 
                         "Total Students": student_count,
                         "Average Math Score": avg_math_score,
                         "Average Reading Score": avg_read_score,
                         "% Passing Math" : math_pass_percent,
                         "% Passing Reading": read_pass_percent,
                         "% Overall Passing Rate": ((math_pass_percent+ read_pass_percent)/2)
                              })

school_type = sch_df[["name","type","budget"]]
school_type = school_type.set_index("name")
school_summary = school_summary.merge(school_type, left_index=True, right_index=True)
school_summary = school_summary.rename(columns={"budget": "Total School Budget",
                                     "type": "School Type"})  

school_summary["Per Student Budget"] = school_summary["Total School Budget"]/school_summary["Total Students"]

school_summary = school_summary[["School Type","Total Students", "Total School Budget", "Per Student Budget","Average Math Score", 
               "Average Reading Score","% Passing Math","% Passing Reading", "% Overall Passing Rate" ]]
school_summary.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.05</td>
      <td>81.03</td>
      <td>66.68</td>
      <td>81.93</td>
      <td>74.31</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>94.13</td>
      <td>97.04</td>
      <td>95.59</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>65.99</td>
      <td>80.74</td>
      <td>73.36</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>68.31</td>
      <td>79.30</td>
      <td>73.80</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>93.39</td>
      <td>97.14</td>
      <td>95.27</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.29</td>
      <td>80.93</td>
      <td>66.75</td>
      <td>80.86</td>
      <td>73.81</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.80</td>
      <td>83.81</td>
      <td>92.51</td>
      <td>96.25</td>
      <td>94.38</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>65.68</td>
      <td>81.32</td>
      <td>73.50</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>66.06</td>
      <td>81.22</td>
      <td>73.64</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>94.59</td>
      <td>95.95</td>
      <td>95.27</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>66.37</td>
      <td>80.22</td>
      <td>73.29</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.36</td>
      <td>83.73</td>
      <td>93.87</td>
      <td>95.85</td>
      <td>94.86</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>93.27</td>
      <td>97.31</td>
      <td>95.29</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>93.87</td>
      <td>96.54</td>
      <td>95.20</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.68</td>
      <td>83.96</td>
      <td>93.33</td>
      <td>96.61</td>
      <td>94.97</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Top Performing Schools (By Passing Rate) - Top 5
school_summary.sort_values("% Overall Passing Rate",ascending=False).head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>95.586652</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>95.290520</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>95.265668</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>95.203679</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Poor Performing Schools (By Passing Rate) - Bottom 5
school_summary.sort_values("% Overall Passing Rate",ascending=False).tail()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>73.804308</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>73.639992</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>73.500171</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>73.293323</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Math Scores by Grade
stu_grade_group = stu_df.groupby(["school","grade"])["math_score"].mean()
stu_grade_df = pd.DataFrame([stu_grade_group]).transpose()
stu_grade_df.reset_index(inplace=True)
stu_grade_df = stu_grade_df.pivot(index="school", columns="grade", values="math_score")[["9th","10th","11th","12th"]]
stu_grade_df.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.08</td>
      <td>77.00</td>
      <td>77.52</td>
      <td>76.49</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.09</td>
      <td>83.15</td>
      <td>82.77</td>
      <td>83.28</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.40</td>
      <td>76.54</td>
      <td>76.88</td>
      <td>77.15</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.36</td>
      <td>77.67</td>
      <td>76.92</td>
      <td>76.18</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.04</td>
      <td>84.23</td>
      <td>83.84</td>
      <td>83.36</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.44</td>
      <td>77.34</td>
      <td>77.14</td>
      <td>77.19</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.79</td>
      <td>83.43</td>
      <td>85.00</td>
      <td>82.86</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.03</td>
      <td>75.91</td>
      <td>76.45</td>
      <td>77.23</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.19</td>
      <td>76.69</td>
      <td>77.49</td>
      <td>76.86</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.63</td>
      <td>83.37</td>
      <td>84.33</td>
      <td>84.12</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.86</td>
      <td>76.61</td>
      <td>76.40</td>
      <td>77.69</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.42</td>
      <td>82.92</td>
      <td>83.38</td>
      <td>83.78</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.59</td>
      <td>83.09</td>
      <td>83.50</td>
      <td>83.50</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.09</td>
      <td>83.72</td>
      <td>83.20</td>
      <td>83.04</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.26</td>
      <td>84.01</td>
      <td>83.84</td>
      <td>83.64</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Reading Scores by Grade
stu_grade_group = stu_df.groupby(["school","grade"])["reading_score"].mean()
stu_grade_df = pd.DataFrame([stu_grade_group]).transpose()
stu_grade_df.reset_index(inplace=True)
stu_grade_df = stu_grade_df.pivot(index="school", columns="grade", values="reading_score")[["9th","10th","11th","12th"]]
stu_grade_df.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.30</td>
      <td>80.91</td>
      <td>80.95</td>
      <td>80.91</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.68</td>
      <td>84.25</td>
      <td>83.79</td>
      <td>84.29</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.20</td>
      <td>81.41</td>
      <td>80.64</td>
      <td>81.38</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.63</td>
      <td>81.26</td>
      <td>80.40</td>
      <td>80.66</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.37</td>
      <td>83.71</td>
      <td>84.29</td>
      <td>84.01</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.87</td>
      <td>80.66</td>
      <td>81.40</td>
      <td>80.86</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.68</td>
      <td>83.32</td>
      <td>83.82</td>
      <td>84.70</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.29</td>
      <td>81.51</td>
      <td>81.42</td>
      <td>80.31</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.26</td>
      <td>80.77</td>
      <td>80.62</td>
      <td>81.23</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.81</td>
      <td>83.61</td>
      <td>84.34</td>
      <td>84.59</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.99</td>
      <td>80.63</td>
      <td>80.86</td>
      <td>80.38</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.12</td>
      <td>83.44</td>
      <td>84.37</td>
      <td>82.78</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.73</td>
      <td>84.25</td>
      <td>83.59</td>
      <td>83.83</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.94</td>
      <td>84.02</td>
      <td>83.76</td>
      <td>84.32</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.83</td>
      <td>83.81</td>
      <td>84.16</td>
      <td>84.07</td>
    </tr>
  </tbody>
</table>
</div>




```python
# min and max for setting the bin values
# school_summary["Per Student Budget"].max()
# school_summary["Per Student Budget"].max()
#Scores by School Spending
# Set bins and their respective labels
bins = [0,585,615,645,675]
group_names = ["<$585","$585-615","$615-645","$645-675"]
# Add a new column and set values based on the bins
school_summary["Spending Range"] = pd.cut(school_summary["Per Student Budget"], bins, labels=group_names)
# Group data based on the bins
spend_group = school_summary.groupby("Spending Range").mean()
spend_group = spend_group[["Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","% Overall Passing Rate"]]
spend_group.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Range</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;$585</th>
      <td>83.46</td>
      <td>83.93</td>
      <td>93.46</td>
      <td>96.61</td>
      <td>95.04</td>
    </tr>
    <tr>
      <th>$585-615</th>
      <td>83.60</td>
      <td>83.89</td>
      <td>94.23</td>
      <td>95.90</td>
      <td>95.07</td>
    </tr>
    <tr>
      <th>$615-645</th>
      <td>79.08</td>
      <td>81.89</td>
      <td>75.67</td>
      <td>86.11</td>
      <td>80.89</td>
    </tr>
    <tr>
      <th>$645-675</th>
      <td>77.00</td>
      <td>81.03</td>
      <td>66.16</td>
      <td>81.13</td>
      <td>73.65</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get min and max to set the bin values
# school_summary["Total Students"].max()
# school_summary["Total Students"].max()
#Scores by School Size
# Set bins and their respective labels
bins = [0,1000,2000,5000]
group_names = ["Small(<1000)","Medium(1000-2000)","Large(2000-5000)"]
# Add a new column and set values based on the bins 
school_summary["School Size"] = pd.cut(school_summary["Total Students"], bins, labels=group_names)
# Group data based on the bins
school_size_group = school_summary.groupby("School Size").mean()
school_size_group = school_size_group[["Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","% Overall Passing Rate"]]
school_size_group.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small(&lt;1000)</th>
      <td>83.82</td>
      <td>83.93</td>
      <td>93.55</td>
      <td>96.10</td>
      <td>94.82</td>
    </tr>
    <tr>
      <th>Medium(1000-2000)</th>
      <td>83.37</td>
      <td>83.86</td>
      <td>93.60</td>
      <td>96.79</td>
      <td>95.20</td>
    </tr>
    <tr>
      <th>Large(2000-5000)</th>
      <td>77.75</td>
      <td>81.34</td>
      <td>69.96</td>
      <td>82.77</td>
      <td>76.36</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Scores by School Type
school_type_summary = school_summary.groupby("School Type").mean()
school_type_summary = school_type_summary[["Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","% Overall Passing Rate"]]
school_type_summary.round(2)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.47</td>
      <td>83.90</td>
      <td>93.62</td>
      <td>96.59</td>
      <td>95.10</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.96</td>
      <td>80.97</td>
      <td>66.55</td>
      <td>80.80</td>
      <td>73.67</td>
    </tr>
  </tbody>
</table>
</div>


