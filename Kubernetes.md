# Kubeadm

# Kubebuilder

- Operator就是Controller和CRD。CRD定义了每个Operator需要创建和管理的自定义资源对象，底层通过API Server接口在ETCD中注册一种新的资源。注册完成后ControllerManager会通过Client Go监控到Kubernetes集群的资源对象发生改变，然后调用我们自己开发的Controller实施对应的操作。
    - 因此。Operator的运行机制就是将CRD注册到ControllerManager，通过ListAll-Watch机制监听资源的变化，然后在周期内实现各个环节的协调。
    - Operator框架包含三个组件：SDK、生命周期管理和Metering。

# Kubernetes

# Docker