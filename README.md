# pytorch0729


### 로그변환, 정규화
<br>
~~~python
def dB( magnitude ):
    return 20*np.log10( np.abs(magnitude) + np.finfo(np.float32).eps )
    


def Mag_normalization( L, R ):

    Mag = np.asarray( [ L, R ] )
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

<br>
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


    # """정규화X"""
    # x_element.append( mag_L )
    # x_element.append( mag_R )
    # x_element.append( phi_L )
    # x_element.append( phi_R )



    """정규화"""
    #x_left, x_right = Mag_normalization( log_L, log_R )   # log[dB] scale변환 후 정규화
    x_left, x_right = Mag_normalization( mag_L , mag_R )  # log[dB] scale변환 하지 않고 정규화
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


### Screenshots
epoch=100
batch_size=20
lr=0.00002

<table>
  <tr> <br>log변환 X, 정규화 X
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2903.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2903.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2903.png", height=200px, width=250px>  </td>
  </tr>
  
  <tr> training accuracy: 93%<br>
       validation accuracy: 71%<br>
  </tr>
  
    <tr> <br>log변환 X, 정규화 O
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2904.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2904.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2904.png", height=200px, width=250px>  </td>
  </tr>
  
  <tr> training accuracy: 87.875%<br>
       validation accuracy: 53.3%<br>
  </tr>
  
    <tr> <br>log변환 O, 정규화 X
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2906.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2906.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2906.png", height=200px, width=250px>  </td>
  </tr>
  
  <tr> training accuracy: 98.5%<br>
       validation accuracy: 55.5%<br>
  </tr>
  
    <tr> <br>log변환 O, 정규화 O
  </tr>

  <tr>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/train_dataset_confusion_matrix2905.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/validation_dataset_confusion_matrix2905.png", height=200px, width=250px>  </td>
    <td> <img src="https://github.com/Kang-Dong-Hwi/pytorch0729/blob/master/Screenshots/Adam2905.png", height=200px, width=250px>  </td>
  </tr>
  
  <tr> training accuracy: 82%<br>
       validation accuracy: 11%<br>
  </tr>


</table>
