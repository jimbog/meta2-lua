# META II implemented in Lua

META II is a domain specific language (DSL) for describing compilers. The META
II compiler takes as input the DSL and outputs a compiler. The META II compiler
can be described in this DSL.

Started from http://loup-vaillant.fr/projects/metacompilers/

## Description of META II compiler in META II
```
.syntax program

  output   = '{'
             * ( '$'      {'io.write(_input)'}
               | .string  {'io.write(' $  ')'})
             '}'          {'io.write("\\n")' };

  primary  = .id       { $ '()'                         }
           | .string   {'_run.testSTR(' $ ')'           }
           | '.id'     {'local _input = _run.parse("^%a[%a%d]*")' }
           | '.number' {'local _input = _run.parse("^%d+")'}
           | '.string' {'local _input = _run.parseSTR()'}
           | '.empty'  {'_switch = true'                }
           | '(' choice ')'
           | '*'       {'repeat'                        }
             primary   {'until not _switch'             }
                       {'_switch = true'                };

  sequence = {'repeat'    }
               (primary {'if not _switch then break   end'} | output)
             * (primary {'if not _switch then error() end'} | output)
             {'until true'};

  choice   = {'repeat'    }
             sequence * ('|' {'if _switch then break end'} sequence)
             {'until true'};

  rule     = .id {'function ' $ '()'} '=' choice ';' {'end'};

  program  = '.syntax' .id {'local _run = require("runtime")'}
             * rule '.end' {$ '()'                           };

.end
```
