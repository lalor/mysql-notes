
# Session

### step 1

在 mysqld.cc 中的有一个名为global_system_variables的全局变量，该变量保存了所有my.cnf中配置变量的值


	system_variables global_system_variables;

修改system_variables，增加一个变量。


### step 2

在mysqld.cc中，my_long_options 数组最后增加一项：

	struct my_option my_long_options [] = {
		//绑定变量与选项
  		{"laimingxing-age", OPT_LAIMINGXING, "This is a option just for test", &global_system_variables.opt_laimingxing_age, &global_system_variables.opt_laimingxing_age, 0, GET_ULONG, REQUIRED_ARG, 2, 1, 1000, 0, 1, 0},
	};

### step 3

其中，OPT_LAIMINGXING 是配置相关的枚举值，直接在mysqld.h中找到enum options_mysqld添加即可：

### step 4

        static Sys_var_ulong Sys_laimingxing_age( "options_laimingxing_age",
                "The maximum length of the result of function GROUP_CONCAT()",
                SESSION_VAR(laimingxing_age_var),
                CMD_LINE(REQUIRED_ARG),
                VALID_RANGE(1, 65535),
                DEFAULT(1),
                BLOCK_SIZE(1));

### step 5 初始化

	main --> init_common_variables --> get_options --> handle_options --> my_handle_options(my_getopt.cc)

**总结：**

* 在system_variables中增加一个变量，保存配置中的值，保存通过mysql客户端修改过后的值
* 在my_long_options数组最后增加一个选项，该选项根据MySQL定义的规则编写，在增加选项的时候，绑定了选项与变量，MySQL配置解析函数会自动根据选项配置，判断取值是否合法，并将配置文件中的值读入，保存到变量（system_variables）中
* 配置文件在init_common_variables 函数别调用以后生效，配置只能在该函数之后使用

### others

        /**
          @file
          Definitions of all server's session or global variables.

          How to add new variables:

          1. copy one of the existing variables, and edit the declaration.
          2. if you need special behavior on assignment or additional checks
             use ON_CHECK and ON_UPDATE callbacks.
          3. *Don't* add new Sys_var classes or uncle Occam will come
             with his razor to haunt you at nights

          Note - all storage engine variables (for example myisam_whatever)
          should go into the corresponding storage engine sources
          (for example in storage/myisam/ha_myisam.cc) !
        */

