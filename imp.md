The IMP-SEMANTICS
=================

These are the semantics of IMP

```maude

set include BOOL off .

fmod FVP-NAT is
  sort Bool .
  op true  : -> Bool [ctor metadata "0"] .
  op false : -> Bool [ctor metadata "1"] .

  sort Nat NzNat .
  subsort NzNat < Nat .
  op 0 : -> Nat   [ctor metadata "2"] .
  op 1 : -> NzNat [ctor metadata "3"] .
  op _+_ : Nat Nat   ->   Nat [ctor assoc comm id: 0 metadata "4"] .
  op _+_ : NzNat Nat -> NzNat [ctor ditto            metadata "4"] .

  var N M : Nat .
  var Z   : NzNat .

  op _monus_ : Nat Nat -> Nat [metadata "5"] .
  eq  N      monus (N + M) = 0 [variant] .
  eq (N + Z) monus  N      = Z [variant] .

  op _<=_ : Nat Nat -> Bool [metadata "6"] .
  eq N     <= N + M = true  [variant] .
  eq N + Z <= N     = false [variant] .

  op _<_ : Nat Nat -> Bool [metadata "7"] .
  eq N     < N + Z = true  [variant] .
  eq N + M < N     = false [variant] .

  op sd : Nat Nat -> Nat [comm metadata "8"] .
  eq sd(N + Z, N    ) = Z [variant] .
  eq sd(N    , N + M) = M [variant] .
endfm

fmod FVP-NAT-MULT is
  pr FVP-NAT .

  var N : Nat . var NzN NzN' : NzNat .

  op _*_ : Nat Nat -> Nat [assoc comm metadata "9"] .
  ---------------------------------------------------
  eq N * 0 =  0 [variant] .
  eq N * 1 =  N [variant] .

  eq N * (NzN + NzN') = (N * NzN) + (N * NzN') .
endfm

fmod IMP-LIST is
  pr FVP-NAT .
  sort List .
  subsort Nat < List .

  op nil      :           -> List    [ctor metadata "10"] .
  op _$_      : List List -> List    [ctor id: nil assoc metadata "11"] .

  vars N X Y : Nat .
  var L L' : List .

  --- operations

  op head : List -> List               [metadata "13"] .

  eq head(nil) = nil .
  eq head(N $ L) = N .

  op tail : List -> List               [metadata "14"] .

  eq tail(nil) = nil .
  eq tail(N $ L) = L .

  op isEmpty  : List -> Bool    [metadata "15"] .

  eq isEmpty(nil) = true .
  eq isEmpty(N $ L) = false .

endfm

fmod IMP-DATA is
  pr IMP-LIST .
  sort Id .
  op a  :    -> Id [ctor metadata "15"] .
  op b  :    -> Id [ctor metadata "16"] .
  op c  :    -> Id [ctor metadata "17"] .
  op i  :    -> Id [ctor metadata "18"] .
  op j  :    -> Id [ctor metadata "19"] .
  op k  :    -> Id [ctor metadata "20"] .
  op x  :    -> Id [ctor metadata "21"] .
  op y  :    -> Id [ctor metadata "22"] .
  op z  :    -> Id [ctor metadata "23"] .
  op _, : Id -> Id [ctor metadata "24"] .
endfm

fmod IMP-DATA+MUL is
  pr IMP-DATA .
  pr FVP-NAT-MULT .
endfm

fmod IMP-SYNTAX is
  pr IMP-DATA .
  sort LExp AExp BExp Exp Stmt Ids Value .
  subsort Id Nat < AExp .
  subsort Id List < LExp .
  subsort Bool < BExp .
  subsort BExp AExp LExp < Exp .
  subsort Bool Nat List < Value < Exp .

  --- ctors
  op __              : Stmt Stmt      -> Stmt  [ctor prec 42 gather (E e) metadata "25"] .
  op if (_) _ else _ : BExp Stmt Stmt -> Stmt  [ctor metadata "26" ] .
  op while (_) _     : BExp Stmt      -> Stmt  [ctor metadata "27"] .
  op {_}             : Stmt           -> Stmt  [ctor metadata "28"] .
  op {}              :                -> Stmt  [ctor metadata "29"] .
  op _=_;            : Id AExp        -> Stmt  [ctor metadata "30"] .
  op _=_;            : Id LExp        -> Stmt  [ctor metadata "30"] .
  op _+:_            : AExp AExp      -> AExp  [ctor metadata "31"] .
  op _-:_            : AExp AExp      -> AExp  [ctor metadata "32"] .
  op _*:_            : AExp AExp      -> AExp  [ctor metadata "33"] .
  op !_              : BExp           -> BExp  [ctor metadata "34"] .
  op _<:_            : AExp AExp      -> BExp  [ctor metadata "35"] .
  op _&&:_           : BExp BExp      -> BExp  [ctor metadata "36"] .
  op _++list_        : LExp LExp      -> LExp  [ctor metadata "37"] .
  op first (_)       : LExp           -> LExp  [ctor metadata "38"] .
  op last  (_)       : LExp           -> LExp  [ctor metadata "39"] .
  op empty (_)       : LExp           -> BExp  [ctor metadata "40"] .
  --- defined
  op val?            : Exp            -> Bool [metadata "37"] .
  ---
  var A A' : AExp . var B B' : BExp . var L L' : LExp .
  ---
  eq val?(Q:Id)        = false [variant] .
  eq val?(A +: A')     = false [variant] .
  eq val?(A *: A')     = false [variant] .
  eq val?(A -: A')     = false [variant] .
  eq val?(! B)         = false [variant] .
  eq val?(A <: A')     = false [variant] .
  eq val?(B &&: B')    = false [variant] .
  eq val?(L ++list L') = false [variant] .
  eq val?(first(L))    = false [variant] .
  eq val?(last(L))     = false [variant] .
  eq val?(empty(L))    = false [variant] .
  eq val?(V:Value)     = true  [variant] .
endfm

fmod IMP-SYNTAX+MUL is
  pr IMP-SYNTAX .
  pr IMP-DATA+MUL .
endfm

fmod IMP-HOLE-SYNTAX is
  pr IMP-SYNTAX .
  sort !AExp !BExp !LExp !Stmt .
  --- !AExp
  op []+:_  : AExp -> !AExp [ctor metadata "38"] .
  op _+:[]  : AExp -> !AExp [ctor metadata "39"] .
  op []*:_  : AExp -> !AExp [ctor metadata "40"] .
  op _*:[]  : AExp -> !AExp [ctor metadata "41"] .
  op []-:_  : AExp -> !AExp [ctor metadata "42"] .
  op _-:[]  : AExp -> !AExp [ctor metadata "43"] .
  --- !BExp
  op ![]    : -> !BExp         [ctor metadata "44"] .
  op []<:_  : AExp -> !BExp    [ctor metadata "45"] .
  op _<:[]  : Nat -> !BExp     [ctor metadata "46"] .
  op []&&:_ : BExp -> !BExp    [ctor metadata "47"] .
  op empty([]) : -> !BExp      [ctor metadata "47"] .
  --- !LExp
  op []++list_ : LExp -> !LExp    [ctor metadata "47"] .
  op _++list[] : LExp -> !LExp    [ctor metadata "47"] .
  op first([]) : -> !LExp    [ctor metadata "47"] .
  op last([])  : -> !LExp    [ctor metadata "47"] . 
  --- !Stmt
  op if ([]) _ else _ : Stmt Stmt -> !Stmt [ctor metadata "48"] .
  op while ([]) _     : Stmt      -> !Stmt [ctor metadata "49"] .
  op _=[];            : Id        -> !Stmt [ctor metadata "50"] .
endfm

fmod ENVIRONMENT is
  pr IMP-DATA .
  sort Env .
  sort VEnv TEnv WrappedEnv .
  sort Type .
  --- Type
  op TNat   : -> Type [ctor metadata "46"] .
  op TList  : -> Type [ctor metadata "47"] .

  --- Env
  op _*_    : TEnv TEnv -> TEnv [ctor prec 51 assoc comm id: mtTE metadata "48"] .
  op _*_    : VEnv VEnv -> VEnv [ctor prec 51 assoc comm id: mtVE metadata "49"] .
  op _|->_  : Id Nat  -> VEnv [ctor prec 50 metadata "50"] .
  op _|->_  : Id List -> VEnv [ctor prec 50 metadata "51"] .
  op _|->_  : Id Type -> TEnv [ctor prec 50 metadata "52"] .
  op _&_    : TEnv VEnv -> Env [ctor metadata "53"] .
  op mt     : -> Env [ctor metadata "54"] .
  op mtVE   : -> VEnv [ctor metadata "55"] .
  op mtTE   : -> TEnv [ctor metadata "56"] .
  op {_}    : Env -> WrappedEnv [ctor metadata "57"] .
endfm

mod IMP-FVP-FRAGMENT is
  pr IMP-HOLE-SYNTAX .
  pr ENVIRONMENT .

  sort RedContext Continuation Redex .
  subsort Stmt Exp !LExp !AExp !BExp !Stmt < Redex .

  --- RedContext
  op <_|_>  : Continuation Env -> RedContext [ctor metadata "58"] .
  --- Continuation
  op _~>_   : Redex Continuation -> Continuation [ctor prec 43 metadata "59"] .
  op done   : -> Continuation [ctor metadata "60"] .
endm

mod IMP-SEMANTICS is
  pr IMP-FVP-FRAGMENT .
  pr IMP-SYNTAX .
  pr IMP-SYNTAX+MUL .

  var K : Continuation .
  var E : Env .
  var TE : TEnv .
  var VE : VEnv .
  var TY : Type .
  var S S' : Stmt .
  var Q : Id .
  var BE BE' : BExp .
  var AE AE' : AExp .
  var LE LE' : LExp .
  var N M X Y X' : Nat .
  var B : Bool .
  var L L' : List .

  --- Rules
  --- Heating Rules
 crl [#if]        : < if (BE) S else S' ~> K | E >  => < BE ~> if ([]) S else S' ~> K | E > if val?(BE) = false .
 crl [#assign-ae] : < (Q = AE ;) ~> K | E >         => < AE ~> Q = [];    ~> K | E >        if val?(AE) = false .
 crl [#assign-le] : < (Q = LE ;) ~> K | E >         => < LE ~> Q = [];    ~> K | E >        if val?(LE) = false .
 crl [#add-lft]   : < AE +:  AE' ~> K | E >         => < AE ~> [] +: AE'  ~> K | E >        if val?(AE) = false .
 crl [#add-rght]  : < N  +:  AE  ~> K | E >         => < AE ~> N  +: []   ~> K | E >        if val?(AE) = false .
 crl [#mul-lft]   : < AE *:  AE' ~> K | E >         => < AE ~> [] *: AE'  ~> K | E >        if val?(AE) = false .
 crl [#mul-rght]  : < N  *:  AE  ~> K | E >         => < AE ~> N  *: []   ~> K | E >        if val?(AE) = false .
 crl [#sub-lft]   : < AE -:  AE' ~> K | E >         => < AE ~> [] -: AE'  ~> K | E >        if val?(AE) = false .
 crl [#sub-rght]  : < N  -:  AE  ~> K | E >         => < AE ~> N  -: []   ~> K | E >        if val?(AE) = false .
 crl [#and]       : < BE &&: BE' ~> K | E >         => < BE ~> [] &&: BE' ~> K | E >        if val?(BE) = false .
 crl [#lt-lft]    : < AE <:  AE' ~> K | E >         => < AE ~> [] <: AE'  ~> K | E >        if val?(AE) = false .
 crl [#lt-rght]   : < N  <:  AE  ~> K | E >         => < AE ~> N  <: []   ~> K | E >        if val?(AE) = false .
 crl [#not]       : < ! BE       ~> K | E >         => < BE ~> ! []       ~> K | E >        if val?(BE) = false .
 crl [#list-conc-left] : < LE ++list LE' ~> K | E >      => < LE ~> [] ++list LE' ~> K | E >     if val?(LE) = false .
 crl [#list-conc-rght] : < LE' ++list LE ~> K | E >      => < LE ~> LE' ++list [] ~> K | E >     if val?(LE) = false .
 crl [#list-first] : < first(LE)         ~> K | E >      => < LE ~> first([])     ~> K | E >     if val?(LE) = false .
 crl [#list-last]  : < last(LE)          ~> K | E >      => < LE ~> last([])      ~> K | E >     if val?(LE) = false .
 crl [#list-empty] : < empty(LE)         ~> K | E >      => < LE ~> empty([])     ~> K | E >     if val?(LE) = false .
  --- Cooling Rules
  rl [@if]        : < B  ~> if ([]) S else S' ~> K | E > => < if (B) S else S' ~> K | E > .
  rl [@assign-ae] : < N  ~> Q = [];   ~> K | E >     => < (Q = N ;) ~> K | E > .
  rl [@assign-le] : < L  ~> Q = [];   ~> K | E >     => < (Q = L ;) ~> K | E > .
  rl [@add-lft]   : < N  ~> [] +: AE  ~> K | E >     => < N  +: AE  ~> K | E > .
  rl [@add-rght]  : < M  ~> N  +: []  ~> K | E >     => < N  +: M   ~> K | E > .
  rl [@mul-lft]   : < N  ~> [] *: AE  ~> K | E >     => < N  *: AE  ~> K | E > .
  rl [@mul-rght]  : < M  ~> N  *: []  ~> K | E >     => < N  *: M   ~> K | E > .
  rl [@sub-lft]   : < N  ~> [] -: AE  ~> K | E >     => < N  -: AE  ~> K | E > .
  rl [@sub-rght]  : < M  ~> N  -: []  ~> K | E >     => < N  -: M   ~> K | E > .
  rl [@and]       : < B  ~> [] &&: BE ~> K | E >     => < B &&: BE  ~> K | E > .
  rl [@lt-lft]    : < N  ~> [] <: AE  ~> K | E >     => < N <: AE   ~> K | E > .
  rl [@lt-rght]   : < M ~> N  <: []   ~> K | E >     => < N <: M    ~> K | E > .
  rl [@not]       : < B  ~> ! []      ~> K | E >     => < ! B       ~> K | E > .
  rl [@list-conc-left]  : < L  ~> [] ++list LE  ~> K | E >  => < L ++list LE ~> K | E > .
  rl [@list-conc-rght]  : < L  ~> LE ++list []  ~> K | E >  => < LE ++list L ~> K | E > .
  rl [@list-first]      : < L  ~> first([])     ~> K | E >  => < first(L)    ~> K | E > .
  rl [@list-last]       : < L  ~> last([])      ~> K | E >  => < last(L)     ~> K | E > .
  rl [@list-empty]      : < L  ~> empty([])     ~> K | E >  => < empty(L)    ~> K | E > .
  --- Semantic Rules
  --- Stmts
  rl [stmtlist]  : < S S' ~> K | E >                 => < S ~> S' ~> K | E > .
  rl [block]     : < {S} ~> K | E >                  => < S ~> K | E > .
  rl [emp-block] : < {}  ~> K | E >                  => < K | E > .
  rl [if-true]   : < if (true) S else S' ~> K | E >  => < S  ~> K | E > .
  rl [if-false]  : < if (false) S else S' ~> K | E > => < S' ~> K | E > .
  rl [while]     : < while (BE) {S} ~> K | E >       => < if (BE) {S while (BE) {S}} else {} ~> K | E > .
  --- Assignemnt/lookup rules assume memory locations exist and are unique
  rl [assign-nat]    : < (Q = N ;) ~> K | (TE * (Q |-> TNat)) & (VE * (Q |-> M)) >  => < K | (TE * (Q |-> TNat)) & (VE * (Q |-> N)) > .
  rl [assign-list]   : < (Q = L ;) ~> K | (TE * (Q |-> TList)) & (VE * (Q |-> L')) >  => < K | (TE * (Q |-> TList)) & (VE * (Q |-> L)) > .
  rl [lookup-nat]    : < Q ~> K | (TE * (Q |-> TNat)) & (VE * (Q |-> N)) >          => < N ~> K | (TE * (Q |-> TNat)) & (VE * (Q |-> N)) > .
  rl [lookup-list]   : < Q ~> K | (TE * (Q |-> TList)) & (VE * (Q |-> L)) >          => < L ~> K | (TE * (Q |-> TList)) & (VE * (Q |-> L)) > .
  --- Exps
  rl [add]        : < N +: M       ~> K | E >        => < N + M        ~> K | E > .
  rl [mul]        : < N *: M       ~> K | E >        => < N * M        ~> K | E > .
  rl [sub]        : < N -: M       ~> K | E >        => < sd(N,M)      ~> K | E > .
  rl [lt]         : < N <: M       ~> K | E >        => < N < M        ~> K | E > .
  rl [not-1]      : < ! true       ~> K | E >        => < false        ~> K | E > .
  rl [not-2]      : < ! false      ~> K | E >        => < true         ~> K | E > .
  rl [and-true]   : < true  &&: BE ~> K | E >        => < BE           ~> K | E > .
  rl [and-false]  : < false &&: BE ~> K | E >        => < false        ~> K | E > .
  rl [list-conc]  : < L ++list L'  ~> K | E >        => < L $ L'       ~> K | E > .
  rl [list-first] : < first(L)     ~> K | E >        => < head(L)      ~> K | E > .
  rl [list-last]  : < last(L)      ~> K | E >        => < tail(L)      ~> K | E > .
  rl [list-empty] : < empty(L)     ~> K | E >        => < isEmpty(L)   ~> K | E > .
endm

mod NONSEQ-IMP-SEMANTICS is
  pr IMP-SEMANTICS .

  var AE AE' : AExp .
  var E : Env .
  var N : Nat .
  var K : Continuation .

 crl [@add-rght] : < AE +: AE' ~> K | E >         => < AE' ~> AE +: []   ~> K | E > if val?(AE) = false .
  rl [#add-rght] : < N  ~> AE +: []   ~> K | E >  => < AE +: N  ~> K | E > .
endm

```
