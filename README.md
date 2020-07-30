# pytorch0729

<br><br>

### 로그변환, 정규화
-------
<br>
log scale [dB] 변환,<br>
magnitude 정규화<br>
phase 정규화 함수입니다.<br>

<br>
magnitude 정규화에서는 left(257,382), right(257, 382) array를<br>
(2, 257, 382) arrary로 만든 뒤 정규화했고<br>
<br>
phase 정규화는 left, right 따로 해주었습니다.

~~~python

def dB( magnitude ):
    return 20*np.log10( np.abs(magnitude) + np.finfo(np.float32).eps )
    


def Mag_normalization( L, R ):

    Mag = np.asarray( [ L, R ] )  #(2, 257, 382)
    mu = np.mean( Mag )
    sigma = np.std( Mag )
    z = ( Mag - mu ) / sigma
    return z[0], z[1]



def Phase_normalization( phase ):
    mu = np.mean( phase )
    sigma = np.std( phase )
    
    z = ( phase - mu ) / sigma
    return z
~~~


<br><br><br>



### dataset 구성
----------

<br>
이 전에 코드에서 x_data 구성하는 부분을 다시 해보니

<table>
  <tr>
      <td> <br>0729
      </td>
      <td> <br>0728
      </td>
  </tr>
  <tr>
      <td>
          
~~~python
    x_data = []

    for idx in range( y_data.shape[0] ):

        x_element = []

        mag_L = S_left[:,:,idx]
        mag_R = S_right[:,:,idx]
        phi_L = S_left_phase[:,:,idx]
        phi_R = S_right_phase[:,:,idx]


        """log[dB] scale"""
        log_L = dB( mag_L )
        log_R = dB( mag_R )



        """정규화"""
        x_left, x_right = Mag_normalization( log_L, log_R )   
        # log[dB] scale변환 후 정규화

        x_left_phase    = Phase_normalization( phi_L )
        x_right_phase   = Phase_normalization( phi_R )



        x_element.append( x_left )
        x_element.append( x_right )
        x_element.append( x_left_phase )
        x_element.append( x_right_phase )

        x_data.append( np.asarray(x_element) )

    x_data = np.asarray(x_data)
    y_data = angle_to_digit(y_data)

~~~
   </td>
   <td> 
    
~~~python
      
x_data = []


'''    x_data,       y_data '''
'''(1000,4,257,382), (1000,)'''


for idx in range(1000):

    # list 초기화
    x_element = []


    """ Mag """
    # S_left,  S_right 의 idx번째 array
    x_L = S_L_mag[:,:,idx]
    x_R = S_R_mag[:,:,idx]



    """log scale [dB]"""
    x_L_dB = 20*np.log10( np.abs(x_L) + np.finfo(np.float32).eps )
    x_R_dB = 20*np.log10( np.abs(x_R) + np.finfo(np.float32).eps )
    #x_L = librosa.amplitude_to_db(x_L)
    #x_R = librosa.amplitude_to_db(x_R)



    """phase"""
    # S_left_phase,  S_right_phase 의 idx번째 array
    x_L_phase =  S_L_phase[:,:,idx] 
    x_R_phase =  S_R_phase[:,:,idx] 

    


    """normalization"""
    x_L, x_R = Mag_normalization(x_L_dB, x_R_dB)
    x_L_phase = Phase_normalization( x_L_phase )
    x_R_phase = Phase_normalization( x_R_phase )




    """x_element.shape ==> (4, 257, 382)"""
    # 초기화된 list( x_element )에 
    # left, right의 mag[dB], phase[rad] 총 4개의 array 삽입 
    x_element.append(x_L)
    x_element.append(x_R)
    x_element.append(x_L_phase)
    x_element.append(x_R_phase)



    # x_element 의 type을 list에서 numpy array로 변환후 
    #x_data list에 삽입
    x_data.append( np.asarray(x_element) )



# x_data의 type을 list에서 numpy array로 변환
x_data = np.asarray(x_data)

~~~~
    
   </td>
  </tr>
</table>
    
<br><br><br><br>
    
~~~python

x_data = []

for idx in range( y_data.shape[0] ):
    
    x_element = []
    
    mag_L = S_left[:,:,idx]
    mag_R = S_right[:,:,idx]
    phi_L = S_left_phase[:,:,idx]
    phi_R = S_right_phase[:,:,idx]


    """log[dB] scale"""
    log_L = dB( mag_L )
    log_R = dB( mag_R )



    """정규화"""
    x_left, x_right = Mag_normalization( log_L, log_R )   
    # log[dB] scale변환 후 정규화
    
    x_left_phase    = Phase_normalization( phi_L )
    x_right_phase   = Phase_normalization( phi_R )



    x_element.append( x_left )
    x_element.append( x_right )
    x_element.append( x_left_phase )
    x_element.append( x_right_phase )

    x_data.append( np.asarray(x_element) )

x_data = np.asarray(x_data)
y_data = angle_to_digit(y_data)

~~~

<br><br><br>

### Screenshots
epoch=100<br>
batch_size=20<br>
lr=0.00002<br>

<table>
  <tr> 
      <td colspan="4"> log변환 X, 정규화 X </td>
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2903.png", height=230px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2903.png", height=230px, width=250px>  </td>
    <td colspan="2"> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2903.png", height=200px, width=350px>  </td>
  </tr>
  
  <tr> 
      <td colspan="4">
       training accuracy: 93%<br>
       validation accuracy: 71%<br>
      </td>
  </tr>
  
 
 
 <!-- -->
  <tr> 
      <td colspan="4"><br><br> log변환 X, 정규화 O </td>
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2904.png", height=230px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2904.png", height=230px, width=250px>  </td>
    <td colspan="2"> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2904.png", height=200px, width=350px>  </td>
  </tr>
  
  <tr> 
      <td colspan="4">
       training accuracy: 87.875%<br>
       validation accuracy: 53.3%<br>
      </td>
  </tr>
  
  
  <!-- -->
  <tr> 
      <td colspan="4"><br><br> log변환 O, 정규화 X </td>
  </tr>
  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2906.png", height=230px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2906.png", height=230px, width=250px>  </td>
    <td colspan="2"> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2906.png", height=200px, width=350px>  </td>
  </tr>

  <tr> 
      <td colspan="4">
       training accuracy: 98.5%<br>
       validation accuracy: 55.5%<br>
      </td>
  </tr>
  
  <tr> 
      <td colspan="4"><br><br> log변환 O, 정규화 O </td>
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2905.png", height=230px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2905.png", height=230px, width=250px>  </td>
    <td colspan="2"> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2905.png", height=200px, width=350px>  </td>
  </tr>
  
  <tr> 
      <td colspan="4">
       training accuracy: 82%<br>
       validation accuracy: 11%<br>
      </td>
  </tr>
  
</table>
