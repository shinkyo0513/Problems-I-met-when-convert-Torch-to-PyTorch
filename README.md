# Problems-I-met-when-convert-Torch-to-PyTorch
Problems I met when convert Torch to PyTorch

Basically, I tried to convert a Torch model to a PyTorch model through this (https://github.com/clcarwin/convert_torch_to_pytorch).
However, there are some problems I met during this procedure. I will list them and my solution here.

# At the first step, this converter load the saved torch model by a pytorch function: torch.utils.serialization.load_lua(), so you have to guarantee your pre-saved torch model (consists of model define and maybe pretrained parameters) could be successfully loaded by the function. But I think the current load_lua() is still immature to figure out a complicated torch model.

1. The load_lua() only support torch model defined by torch.nn rather than torch.cudnn, so you have to convert your torch model to nn if it contains some layers defined through cudnn. This code will be used to do this:
        cudnn.convert(your_model, nn)
        
2. The load_lua() cannot convert parameters in cuda, so you have to convert all the parameters to CPU if you moved your model to GPU. This code will do this:
        your_model:float()
        
3. The load_lua() donnot support torch model generated through nn.gModule, so if you have a model combined by several small parts (e.g., sequential module), I suggest that you can convert them to PyTorch separately. For instance,
       seq_nodes = model:findModules('nn.Sequential')
       for i=1, #seq_nodes do
           torch.save(save_name, seq_nodes[i])
       end
       
# The last is about saving Torch model. In some projects, they only save parameters of their model which get from model:getParameters() method. But the convert_torch_to_pytorch.py cannot deal with this parameters directly. Therefore, it is better to save the model directly by torch.save('your/directory/filename', your_model).
       
