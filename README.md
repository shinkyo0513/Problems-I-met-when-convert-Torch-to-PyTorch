# Problems-I-met-when-convert-Torch-to-PyTorch
Problems I met when convert Torch to PyTorch

Basically, I tried to convert a Torch model to a PyTorch model through this (https://github.com/clcarwin/convert_torch_to_pytorch).
However, there are some problems I met during this procedure. I will list the problems and my solutions here.

At the first step, this converter loads the saved torch model by a pytorch method: __torch.utils.serialization.load_lua()__, so you have to guarantee your pre-saved torch model (consists of model define and maybe pretrained parameters) could be successfully loaded by the function. However, the current load_lua() is still immature to figure out a complicated torch model.

1. The load_lua() method only supports torch model defined by torch.nn rather than torch.cudnn, so you have to convert your torch model to nn if it contains some layers defined through cudnn. This code will be used to do this:
'''lua
        cudnn.convert(your_model, nn)
'''
        
2. The load_lua() cannot convert parameters in GPU, so you have to convert all the parameters to CPU before save them. This code will do this:
'''lua
        your_model:float()
'''
        
3. The load_lua() donnot support torch model generated through nn.gModule, so if you have a model combined by several small parts (e.g., sequential module), I suggest that you can save and convert them to PyTorch separately. For instance,
'''lua
       seq_nodes = model:findModules('nn.Sequential')
       for i=1, #seq_nodes do
           torch.save(save_name, seq_nodes[i])
       end
'''
       
The last is about saving Torch model. In some projects, they only save flat parameters of their model which get from model:getParameters() method. But the convert_torch_to_pytorch.py cannot deal with this parameters directly. Therefore, it is better to save the model directly by torch.save('your/directory/filename', your_model).
       
