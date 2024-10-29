---
layout: post
title: "Ways to put 4 different employees into 3 distinguishable rooms"
date: 2024-08-04 05:15:25 -0500
categories: jekyll update
---

### Intro
This is a simple article proving why there are 81 ways to put 4 different employees into 3 distinguishable rooms

### Situations:
1. \$ 4-0-0 \$
2. \$ 3-1-0 \$
3. \$ 2-2-0 \$
4. \$ 2-1-1 \$

### Derivation:
1. \$ 4 - 0 - 0 \$: \$ 3 \$
* You can put all the people in either one of the 3 rooms

2. \$ 3 - 1 - 0 \$: \$ 24 \$
* Calculate all the possible groups of 3 from 4 people using \$ 4 \mathrm{C} 3 \$
* For every combination, the group can choose one of the 3 rooms.  
Therefore \$ \times 3 \mathrm{C} 1 \$
* For every combination, the person left can go into either of the two rooms left.  
Therefore \$ \times 2 \$
* \$ 4 \mathrm{C} 3 \times 3 \mathrm{C} 1 \times 2 = 24\$

3. \$ 2 - 2 - 0 \$: \$ 18 \$
* Calculate all the possible groups of 2 from 4 people using \$ 4 \mathrm{C} 2 \$
* In this case, everytime one group is selected, there will also be another group of 2 left.  
Therefore to omit the repetitive groups, \$ \times \frac{1}{2} \$
* For every combination, the group can choose one of the 3 rooms.  
Therefore \$ \times 3 \mathrm{C} 1 \$
* For every combination, the group left can go into either of the two rooms left.  
Therefore \$ \times 2 \$
* \$ 4 \mathrm{C} 3 \times 3 \mathrm{C} 1 \times 2\$

4. \$ 2 - 1 - 1 \$: \$ 36 \$
* Calculate all the possible groups of 2 from 4 people using \$ 4 \mathrm{C} 2 \$
* For every combination, the group can choose one of the 3 rooms.
Therefore \$ \times 3 \mathrm{C} 1 \$  
* For every combination, the two people left can go to either rooms( as long as not together).  
Therefore \$ \times 2 \$
* \$ 4 \mathrm{C} 2 \times 3 \mathrm{C} 1 \times 2 \$

### Conclusion:
\$$ 3 + 4 \mathrm{C} 3 \times 3 \mathrm{C} 1 \times 2 + 4 \mathrm{C} 3 \times 3 \mathrm{C} 1 \times 2 + 4 \mathrm{C} 2 \times 3 \mathrm{C} 1 \times 2 = 81\$$
