## For(), Own() and Watch() 这三个方法有什么区别？

for是监听谁，等价于Watches(&source.Kind{Type: apiType}, &handler.EnqueueRequestForObject{})

own是要生成谁，等价于Watches(&source.Kind{Type: <ForType-apiType>}, &handler.EnqueueRequestForOwner{OwnerType: apiType, IsController: true})

watch不要用

