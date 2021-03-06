PyTorch implementation of Sparse Shift Layer(SSL) for 3D and 4D tensors  from "All You Need is a Few Shifts: Designing Efficient Convolutional Neural Networks
for Image Classification" (https://arxiv.org/pdf/1903.05285.pdf) 

(**I am not the author** any of mentioned articles, I just implement this for my own purposes)


Shift operation: shifts tensor data(in memory) by indexes. Value and direction of shift are learnable and different between channels.
It might be considered as Zero-FLOP replacement of DepthWise Convolution, wiht 4.5x less memory consumption(in compare wiht 3x3 DepthWise ConvD).

Sparse Shift Layer is quantized and sparse version of Active Shift(https://arxiv.org/pdf/1806.07370.pdf), which in turn learnable version of the GroupedShift(https://arxiv.org/pdf/1711.08141.pdf)

![alt text](https://github.com/DeadAt0m/ActiveSparseShifts-PyTorch/raw/master/shifts.png "Shifts evolution")


Note: by default shift is not circular, it's filling stayed out values(after shift) by 0.

## Requirements:
    PyTorch >= 1.6.0

## Instalation:
    1. Run *python setup.py build_ext* - this builds cpp extensions for CPU and GPU(if detected)
    2. *python setup.py install*  - for install.
    
## Using:
    
    from torchshifts import Shift1D, Shift2D
    shift_layer = Shift2D(in_channels=3)

There is additional options for shift layer:

    padding(str) - Padding added to the input during bilinear interpolation.
                   Allowed: ['zeros', 'border', 'reflect', 'symmetric']. Default: 'zeros'.
    init_stride(float) - Border for uniform initialization of weights(shifts): [-init_stride;init_stride]. Default: 1.
    sparsity_term(float) - Strength of sparsity. Default: 5e-4.
    active_flag(bool) - Unquantized Active Shift like in original article (https://arxiv.org/pdf/1806.07370.pdf). Default: False

## PyTorch Quantization:
   To use shifts with PyTorch Quantization just do following:
   
    from torchshifts.quantized import quant_mapping
    torch.quantization.convert(model_with_Shift_module, ..., mapping=quant_mapping)
    
   Note! That's shift operation itself does not need the requantization and hence activation tracking, because it does not changes tensor values.

## TO DO:
  1. Add unit tests
  2. Fuse 1d and 2d kernel in one function
