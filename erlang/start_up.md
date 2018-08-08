# erlang 启动过程

| name | pid | entry|application|
| ---  | --- | --- | --- |
|init|<0.0.0>|init:boot/1| erts|
|boot|<0.1.0>|erts_code_purger:start/0|erts|
|    |<0.2.0>|erts_literal_area_collector:start/0|erts|
|    |<0.3.0>|erts_dirty_process_code_checker:start/0|erts|
|erl_prim_loader|<0.6.0>|init:do_boot/2 -> erl_prim_loader:start/0|erts|
|error_logger|<0.32.0> |error_logger:start_link/0|kernel|
| application_controller |<0.33.0>|    |kernel|
| |<0.35.0>| application_master:init/4 | kernel|
| |<0.36.0>| application_master:start_link/2|kernel|