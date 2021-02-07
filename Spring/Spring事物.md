# Spring事物
事物分为编程式事物和声明式事物
- 编程式事物:手动提交和回滚事物
- 声明式事物:通过注解获取xml配置,异常自动回滚事物

## Spring事物



## Spring事物7种传播机制
- PROPAGATION_REQUIRED—如果当前有事务，就用当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择(默认传播机制)。
- PROPAGATION_SUPPORTS—支持当前事务，如果当前没有事务，就以非事务方式执行。
- PROPAGATION_MANDATORY—支持当前事务，如果当前没有事务，就抛出异常。 
- PROPAGATION_REQUIRES_NEW—新建事务，如果当前存在事务，把当前事务挂起。 
- PROPAGATION_NOT_SUPPORTED—以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- PROPAGATION_NEVER—以非事务方式执行，如果当前存在事务，则抛出异常。
- PROPAGATION_NESTED—如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则进行与PROPAGATION_REQUIRED类似的操作。