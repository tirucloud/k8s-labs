### Q1: Which of the following is used to separate the key and value in YAML?
```
Colon (:)
```
### Q2: How many array keys are there in the following yaml snippet?
```
Fruits:
  - Orange
  - Apple
  - Banana
Vegetables:
  - Carrot
  - CauliFlower
  - Tomato
```
### Q3: Which of the following statements is true?
* ***A. Dictionary is an unordered collection whereas list is an ordered collection.***
* B. Dictionary is an ordered collection whereas list is an unordered collection.**
* C. Dictionary and list, both are an ordered collection.**
* D. Dictionary and list, both are an unordered collection.**

### Q4: There is a yaml file named practice.yaml under /home/bob/playbooks/ directory with a key property1 and value value1. Add an additional key named property2 and value value2.
There is a yaml file named practice.yaml under /home/bob/playbooks/ directory with a key property1 and value value1.
```
property1: value1
property2: value2
```
### Q5: We have updated the /home/bob/playbooks/practice.yaml file with the key name and value apple. Add some additional properties (given below) to the dictionary.
```
name= apple
color= red
weight= 90g
```
```
name: apple
color: red
weight: 90g
```
### Q6: We have updated the /home/bob/playbooks/practice.yaml file with a dictionary named employee. Add the remaining properties to it using information from the table below.
```
name: apple
color: red
weight: 90g
employee:
  name: john
  gender: male
  age: 24
```
### Q7: Now, update the /home/bob/playbooks/practice.yaml file with a dictionary in dictionary.
Add a dictionary named address to add the address information in this file.

```
employee:
  name: john
  gender: male
  age: 24
  address:
    city: edison
    state: new jersey
    country: united states
```
### Q8: We have updated the /home/bob/playbooks/practice.yaml file with an array of apples. Add a new apple to the list to make it a total of 4.
```
- apple
- apple
- apple
- apple
```
### Q9: In /home/bob/playbooks/practice.yaml, add two more values apple to make it 6.
### Q10: We have updated the /home/bob/playbooks/practice.yaml file with some data for apple, orange and mango. Just like apple, we would like to add additional details for each item, such as color, weight etc. Modify the remaining items to match the below data. 
```
- name: apple
  color: red
  weight: 100g

- name: orange
  color: orange
  weight: 90g

- name: mango
  color: yellow
  weight: 150g
```
### Q11: We have updated the /home/bob/playbooks/practice.yaml file with a dictionary named employee. We would like to record information about multiple employees. Convert the dictionary named employee to an array named employees.
```
employees:
  - name: john
    gender: male
    age: 24
    address:
      city: edison
      state: new jersey
      country: united states

  - name: david
    gender: male
    age: 30
    address:
      city: chicago
      state: illinois
      country: united states
```
### Q12: Update the /home/bob/playbooks/practice.yaml file to add an additional employee (below the existing entry) to the list using the below information.
```
employees:
  - name: john
    gender: male
    age: 24
    address:
      city: edison
      state: new jersey
      country: united states

  - name: sarah
    gender: female
    age: 28
```
### Q13:We have updated the /home/bob/playbooks/practice.yaml file to add some more details. Now add the employee payslip information by adding an array called payslips. Remember, while address is a dictionary, payslips is an array of month and amount.
