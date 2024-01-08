---
hide:
  - navigation
---
# Perovskite Salt Weight Calculator



=== "Main"

    ```python
    ######### user input #########
    MA=1
    Cs=0
    FA=0
    Pb=1
    Br=1.5
    I=1.5
    Cl=0

    Molarity=1 # M
    volume=3 # ml

    ###########

    b=np.array([MA,Cs,FA,Pb,Br,I,Cl])
    give_me_recipe(b,Molarity*volume)

    ```
=== "Defs"

    ```python
    import numpy as np
    import itertools
    import math
    def solver(A,b):
    return np.transpose(np.matmul(np.linalg.inv(A),b))

    def generate_combinations(a, b):
        nums = [1] * a + [0] * (b - a)
        unique_permutations = set(itertools.permutations(nums))
        result = [list(perm) for perm in unique_permutations]
        return result

    def remove_zero_columns(A, b):
        A = np.array(A)
        b = np.array(b)
        non_zero_indices = np.nonzero(b)[0]
        new_A = A[:, non_zero_indices]
        return new_A

    def replace_ones_with_values(a, b):
        a_index = 0
        new_b = []
        for value in b:
            if value == 1:
                new_b.append(a[a_index])
                a_index += 1
            else:
                new_b.append(value)
                
        return new_b

    def compu_reduction(remove_x,A,b,Chemical_List,Element_List):
        n=0
        Chemical_List_remove_index=[]
        for i in Chemical_List:
            if remove_x in i:
                Chemical_List_remove_index.append(n)
            n=n+1
        Chemical_List=np.delete(Chemical_List,Chemical_List_remove_index,axis=0)
        A=np.delete(A,Chemical_List_remove_index,axis=0)
        n=0
        for i in Element_List:
            if remove_x in i:
                A=np.delete(A,n,axis=1)
                b=np.delete(b,n)
            n=n+1
        Element_List.remove(remove_x)
        return A,b,Chemical_List,Element_List

    def give_me_recipe(b,mmol):
        Chemical_List=['MACl','CsCl','FACl','MAI','MABr','CsI','CsBr','FAI','FABr','PbI2','PbBr2','PbCl2']
        Chemical_weight={'MACl':67.52,
                     'CsCl':168.36,
                     'FACl':80.52,
                     'MAI':158.97,
                     'MABr':111.97,
                     'CsI':259.81,
                     'CsBr':212.81,
                     'FAI':172.00,
                     'FABr':125.00,
                     'PbI2':461.00,
                     'PbBr2':367.00,
                     'PbCl2':278.11}
        Element_List0=['MA','Cs','FA','Pb','Br','I','Cl']
        Element_List=['MA','Cs','FA','Pb','Br','I','Cl']
        if abs(b[0]+b[1]+b[2]+2*b[3]-b[4]-b[5]-b[6])>0.0001:
            print('The charge is not balanced.')
    #         print('Are you kidding me?')
        else:
            A=np.array([[1,0,0,0,0,0,1],
                        [0,1,0,0,0,0,1],
                        [0,0,1,0,0,0,1],
                        [1,0,0,0,0,1,0],
                        [1,0,0,0,1,0,0],
                        [0,1,0,0,0,1,0],
                        [0,1,0,0,1,0,0],
                        [0,0,1,0,0,1,0],
                        [0,0,1,0,1,0,0],
                        [0,0,0,1,0,2,0],
                        [0,0,0,1,2,0,0],
                       [0,0,0,1,0,0,2]])
            n=0
            for i in b:
                if i==0:
                    A,b,Chemical_List,Element_List=compu_reduction(Element_List0[n],A,b,Chemical_List,Element_List)
                n=n+1
            
            A=np.transpose(A)
            b=np.transpose(b)
            cb=generate_combinations(np.linalg.matrix_rank(A), A.shape[1])
            para0=[]
            for i in cb:
                new_A = remove_zero_columns(A, i)
                if np.linalg.matrix_rank(new_A)==np.linalg.matrix_rank(A):
                    slt=solver(new_A[:-1],b[:-1])
                    if np.min(slt)>=0:
                        para=replace_ones_with_values(slt, i)
                        para0.append(para)
            para0=np.round(np.array(para0), 5)
            para0=np.unique(para0, axis=0)
            recipe_N=0
            for i in para0:
                n=0
                recipe_N=recipe_N+1
                print('Recipe #',recipe_N)
                for j in i:
                    if j!=0:
                        print(Chemical_List[n],j,',',np.round(j*Chemical_weight[Chemical_List[n]]*mmol, 5),'mg')
                    n=n+1
                print()
    ```

Output:

!!! info "Recipe # 1"
    MABr 1.0 , 335.91 mg

    PbI2 0.75 , 1037.25 mg

    PbBr2 0.25 , 275.25 mg

!!! info "Recipe # 2"
    MAI 1.0 , 476.91 mg

    PbI2 0.25 , 345.75 mg
    
    PbBr2 0.75 , 825.75 mg