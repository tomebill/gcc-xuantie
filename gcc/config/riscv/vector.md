;; Machine description for RISC-V vector operations.
;; Copyright (C) 2019 Free Software Foundation, Inc.
;; Contributed by Jim Wilson (jimw@sifive.com).

;; This file is part of GCC.

;; GCC is free software; you can redistribute it and/or modify
;; it under the terms of the GNU General Public License as published by
;; the Free Software Foundation; either version 3, or (at your option)
;; any later version.

;; GCC is distributed in the hope that it will be useful,
;; but WITHOUT ANY WARRANTY; without even the implied warranty of
;; MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
;; GNU General Public License for more details.

;; You should have received a copy of the GNU General Public License
;; along with GCC; see the file COPYING3.  If not see
;; <http://www.gnu.org/licenses/>.

(include "vector-iterator.md")

;; Operations valid for integer reductions.
(define_code_iterator any_reduc [plus umax smax umin smin and ior xor])

;; Operations valid for floating-point reductions.
(define_code_iterator any_freduc [smax smin])

;; Commutative operation valid for floating-point.
(define_code_iterator any_fcomop [plus mult smax smin])

;; Non-commutative operation valid for floating-point.
(define_code_iterator any_fnoncomop [minus div])

;; All operation valid for floating-point.
(define_code_iterator any_fop [plus mult smax smin minus div])

;; All operation valid for comparison.
(define_code_iterator any_cmp [eq ne lt ltu le leu gt gtu ge geu])

;; All operation valid for signed comparison.
(define_code_iterator signed_cmp [eq ne lt le gt ge])

;; All operation valid for comparison, except ge and geu.
(define_code_iterator cmp_except_ge [eq ne lt ltu le leu gt gtu])

;; All operation valid for widening add and subtract.
(define_code_iterator add_sub [plus minus])

;; All operation valid for <op>not instruction in mask-register logical.
(define_code_iterator any_opnot [and ior])

;; All operation valid for min and max.
(define_code_iterator any_minmax [smin umin smax umax])

;;All operantion valid for floating-point and integer convert.
(define_code_iterator any_fix [fix unsigned_fix])
(define_code_iterator any_float [float unsigned_float])

;; All operation valid for all add.
(define_code_iterator all_plus [plus ss_plus us_plus])

;; All operation valid for all subtract.
(define_code_iterator all_minus [minus ss_minus us_minus])

;; All operation valid for minus and ss_minus.
(define_code_iterator sub_and_ssub [minus ss_minus])

;; <reduc> expands to the name of the reduction that implements a
;; particular code.
(define_code_attr reduc [(plus "sum") (umax "maxu") (smax "max") (umin "minu")
			 (smin "min") (and "and") (ior "or") (xor "xor")])

;; <invmaskop> expand to the name of the insn that inversed binary bit-wise
;; operation for mask type.
(define_code_attr invmaskop [(and "nand") (ior "nor") (xor "xnor")])

;; <vshift> expand to the name of the vector shift that implements a
;; particular code.
(define_code_attr vshift [(ashift "vashl") (ashiftrt "vashr") (lshiftrt "vlshr")])

;; <vshift> expand to the name of the vector narrowing shift that implements a
;; particular code.
(define_code_attr vnshift [(ashiftrt "vnsra") (lshiftrt "vnsrl")])

;; <vmac> expand to the name of the vmacc and vnmsac that implements a
;; particular code.
(define_code_attr vmac [(plus "macc") (minus "nmsac")])

;; <vmadd_sub> expand to the name of the vmadd and vnmsub that implements a
;; particular code.
(define_code_attr vmadd_sub [(plus "madd") (minus "nmsub")])

;; <vfmac> expand to the name of the vfmacc and vfmsac that implements a
;; particular code.
(define_code_attr vfmac [(plus "macc") (minus "msac")])

;; <vfnmac> expand to the name of the vfnmacc and vfnmsac that implements a
;; particular code.
(define_code_attr vfnmac [(plus "nmsac") (minus "nmacc")])

;; <vfmadd_sub> expand to the name of the vfmadd and vfmsub that implements a
;; particular code.
(define_code_attr vfmadd_sub [(plus "madd") (minus "msub")])

;; <vfnmadd_sub> expand to the name of the vfnmadd and vfnmsub that implements a
;; particular code.
(define_code_attr vfnmadd_sub [(plus "nmsub") (minus "nmadd")])

;; <as> expand to the name of the fma and fms that implements a
;; particular code.
(define_code_attr as [(plus "a") (minus "s")])

;; <sa> expand to the name of the fnma and fnms that implements a
;; particular code.
(define_code_attr sa [(plus "s") (minus "a")])

;; <neg_add> expand to the name of saturat instruction that implements a
;; particular code.
(define_code_attr neg_add [(minus "add") (ss_minus "sadd") (us_minus "saddu")])

;; <icmp> expand to the name of the vector integer comparison that implements a
;; particular code.
(define_code_attr icmp [(eq "seq") (ne "sne") (lt "slt") (ltu "sltu") (le "sle")
			(leu "sleu") (gt "sgt") (gtu "sgtu") (ge "sge") (geu "sgeu")])

(define_code_attr ltge [(lt "ltge_") (ltu "ltge_") (ge "ltge_") (geu "ltge_")
			(eq "") (ne "") (le "") (leu "") (gt "") (gtu "") ])

;; <fcmp> expand to the name of the vector floating-point comparison that
;; implements a particular code.
(define_code_attr fcmp [(eq "feq") (ne "fne") (lt "flt") (le "fle")
			(gt "fgt") (ge "fge")])

;; <vshift> expand to the name of the min and max that implements a
;; particular code.
(define_code_attr minmax [(umax "umax") (smax "smax") (umin "umin") (smin "smin")])

(define_code_attr fix_cvt [(fix "fix_trunc") (unsigned_fix "fixuns_trunc")])
(define_code_attr float_cvt [(float "float") (unsigned_float "floatuns")])

;; <sz_op> expand to the name of the wcvt and wcvtu that implements a
;; particular code.
(define_code_attr sz_op [(sign_extend "") (zero_extend "zero_")])
(define_code_attr sz [(sign_extend "s") (zero_extend "z")])

;; <any_fop_type> expand to the pipeline type for the float operation.
(define_code_attr any_fop_type [(plus "vfadd") (minus "vfadd")
				(smax "vfmax") (smin "vfmax")
				(mult "vfmul") (div "vfdiv")])

;; <any_reduc_type> expand to the pipeline type for the vector reduction operation.
(define_code_attr any_reduc_type [(plus "vred_sum") (umax "vred_max")
				  (smax "vred_max") (umin "vred_max")
				  (smin "vred_max") (and "vred_bit")
				  (ior "vred_bit") (xor "vred_bit")])

;; Iterator and attributes for floating-point reduction instructions.
(define_int_iterator FREDUC_REDUC [UNSPEC_REDUC_SUM UNSPEC_ORDERED_REDUC_SUM])

;; Iterator and attributes for widening floating-point reduction instructions.
(define_int_iterator WFREDUC_REDUC [UNSPEC_REDUC_SUM UNSPEC_ORDERED_REDUC_SUM])

;; Iterator and attributes for widening integer reduction instructions.
(define_int_iterator WREDUC_REDUC [UNSPEC_REDUC_SUM UNSPEC_REDUC_USUM])

(define_int_attr sumu [(UNSPEC_REDUC_SUM "") (UNSPEC_REDUC_USUM "u")])

;; Iterator and attributes for misc mask instructions.
(define_int_iterator MISC_MASK_OP [UNSPEC_SBF UNSPEC_SIF UNSPEC_SOF])

(define_int_attr misc_maskop [(UNSPEC_SBF "sbf") (UNSPEC_SIF "sif")
			      (UNSPEC_SOF "sof")])

(define_int_iterator UNSPEC_VMULH [UNSPEC_VMULHS UNSPEC_VMULHU UNSPEC_VMULHSU])

(define_int_attr v_su [(UNSPEC_VMULHS "s") (UNSPEC_VMULHU "u")
		       (UNSPEC_VMULHSU "su") (UNSPEC_VNCLIP "")
		       (UNSPEC_VNCLIPU "u")])

;; Iterator and attributes for sign-injection instructions.
(define_int_iterator UNSPEC_COPYSIGNS [UNSPEC_COPYSIGN UNSPEC_NCOPYSIGN
				       UNSPEC_XORSIGN])

(define_int_attr copysign [(UNSPEC_COPYSIGN "copysign")
			   (UNSPEC_NCOPYSIGN "ncopysign")
			   (UNSPEC_XORSIGN "xorsign")])

(define_int_attr nx [(UNSPEC_COPYSIGN "") (UNSPEC_NCOPYSIGN "n")
		     (UNSPEC_XORSIGN "x")])

;; Iterator and attributes for convert instructions.
(define_int_iterator UNSPEC_FCVT [UNSPEC_LRINT UNSPEC_FCVT_XUF])

(define_int_attr fcvt_xf [(UNSPEC_LRINT "lrint")
			  (UNSPEC_FCVT_XUF "fcvt_xuf")])

(define_int_attr fu [(UNSPEC_LRINT "") (UNSPEC_FCVT_XUF "u")])

;; Iterator and attributes for slide instructions.
(define_int_iterator UNSPEC_VSLIDES [UNSPEC_VSLIDEUP UNSPEC_VSLIDEDOWN])

(define_int_iterator UNSPEC_VSLIDES1 [UNSPEC_VSLIDE1UP UNSPEC_VSLIDE1DOWN])

(define_int_iterator UNSPEC_VFSLIDES1 [UNSPEC_VFSLIDE1UP UNSPEC_VFSLIDE1DOWN])

(define_int_attr ud [(UNSPEC_VSLIDEUP "up") (UNSPEC_VSLIDEDOWN "down")
		     (UNSPEC_VSLIDE1UP "up") (UNSPEC_VSLIDE1DOWN "down")
		     (UNSPEC_VFSLIDE1UP "up") (UNSPEC_VFSLIDE1DOWN "down")])

;; Iterator and attributes for narrowing clip instructions.
(define_int_iterator UNSPEC_VCLIP [UNSPEC_VNCLIP UNSPEC_VNCLIPU])

;; Iterator and attributes for vssrl and vssra instructions.
(define_int_iterator UNSPEC_VSSHIFT [UNSPEC_VSSRL UNSPEC_VSSRA])

(define_int_attr sshift [(UNSPEC_VSSRL "vssrl") (UNSPEC_VSSRA "vssra")])

;; Iterator and attributes for all fixed-point instructions.
(define_int_iterator UNSPEC_SAT_OP [UNSPEC_VAADDU UNSPEC_VAADD
				    UNSPEC_VASUBU UNSPEC_VASUB UNSPEC_VSMUL])

(define_int_attr sat_op [(UNSPEC_VAADDU "vaaddu") (UNSPEC_VAADD "vaadd")
			 (UNSPEC_VASUBU "vasubu") (UNSPEC_VASUB "vasub")
			 (UNSPEC_VSMUL "vsmul")])

;; Iterator and attributes for vsoxei and vsuxei instructions.
(define_int_iterator UNSPEC_INDEXED_STORE [UNSPEC_ORDERED_INDEXED_STORE
					   UNSPEC_UNORDERED_INDEXED_STORE])

(define_int_attr order
 [(UNSPEC_ORDERED_INDEXED_STORE "o") (UNSPEC_UNORDERED_INDEXED_STORE "u")
  (UNSPEC_SEG_LOAD "o") (UNSPEC_SEG_UNORDERED_LOAD "u")
  (UNSPEC_SEG_STORE "o") (UNSPEC_SEG_UNORDERED_STORE "u")
  (UNSPEC_ORDERED_REDUC_SUM "o") (UNSPEC_REDUC_SUM "u")])

;; Iterator and attributes for integer multiply-add instructions.
(define_int_iterator UNSPEC_MASK_VMACS [UNSPEC_MASK_VMADD UNSPEC_MASK_VMSUB
					UNSPEC_MASK_VMACC UNSPEC_MASK_VMSAC])

(define_int_attr imac [(UNSPEC_MASK_VMADD "vmadd")
		       (UNSPEC_MASK_VMSUB "vnmsub")
		       (UNSPEC_MASK_VMACC "vmacc")
		       (UNSPEC_MASK_VMSAC "vnmsac")])

;; Iterator and attributes for ordered and unordered index segment load instructions.
(define_int_iterator UNSPEC_SEG_IDX_LOAD [UNSPEC_SEG_LOAD UNSPEC_SEG_UNORDERED_LOAD])
(define_int_iterator UNSPEC_SEG_IDX_STORE [UNSPEC_SEG_STORE UNSPEC_SEG_UNORDERED_STORE])

;; Vsetvl instructions.

;; These use VIMODES because only the SEW and LMUL matter.  The int/float
;; distinction does not.  Also, the int modes are a superset of the float
;; modes.

(define_expand "riscv_vsetvl<VIMODES:sew>m<VIMODES:lmul>_<P:mode>"
  [(parallel [(set (match_operand:P 0 "register_operand")
		   (unspec:P [(match_operand:P 1 "register_operand")
			      (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL)]
			     UNSPEC_VSETVL))
   (set (reg:SI VL_REGNUM) (unspec:SI [(match_dup 1)
				       (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL)]
				      UNSPEC_VSETVL))
   (set (reg:VIMODES VTYPE_REGNUM) (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL))])]
  "TARGET_VECTOR"
{
})

(define_insn "*riscv_vsetvl<VIMODES:sew>m<VIMODES:lmul>_<P:mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P [(match_operand:P 1 "register_operand" "r")
		   (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL)]
		  UNSPEC_VSETVL))
   (set (reg:SI VL_REGNUM) (unspec:SI [(match_dup 1)
				       (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL)]
				      UNSPEC_VSETVL))
   (set (reg:VIMODES VTYPE_REGNUM) (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL))]
  "TARGET_VECTOR && !(target_subset_version_p (\"v\", 0, 7) && <VIMODES:sew> == 64)"
  { return target_subset_version_p ("v", 0, 7) ? "vsetvli\t%0,%1,e<VIMODES:sew>,m<VIMODES:lmul>" : "vsetvli\t%0,%1,e<VIMODES:sew>,m<VIMODES:lmul>,tu,mu"; }
  [(set_attr "type" "vset")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "vsetvli_x0_<mode>"
  [(set (reg:VIMODES VTYPE_REGNUM) (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL))]
  "target_subset_version_p (\"v\", 0, 7)"
  { return target_subset_version_p ("v", 0, 7) ? "vsetvli\tx0,x0,e<sew>,m<lmul>" : "vsetvli\tx0,x0,e<sew>,m<lmul>,tu,mu"; }
  [(set_attr "type" "vset")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "vsetvlmax<VIMODES:mode>_<P:mode>"
  [(set (reg:VIMODES VTYPE_REGNUM) (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL))
   (set (reg:P VL_REGNUM) (unspec:P [(const_int 0)
				     (unspec:VIMODES [(const_int 0)] UNSPEC_VSETVL)]
				    UNSPEC_VSETVL))
   (set (match_operand:P 0 "register_operand" "=r")
	(reg:P VL_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsetvli\t%0,x0,e<VIMODES:sew>,m<VIMODES:lmul>" : "vsetvli\t%0,x0,e<VIMODES:sew>,m<VIMODES:lmul>,tu,mu"; }
  [(set_attr "type" "vset")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Load/store instructions.

;; Vector Unit-Stride Instructions

(define_expand "vle<VMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(mem:VMODES (match_operand:P 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vle<VMODES:mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=vr")
	(unspec:VMODES
	  [(match_operand:VMODES 1 "memory_operand" "m")
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vle.v\t%0,%1" : "vle<sew>.v\t%0,%1"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vle<VMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mem:VMODES
			  (match_operand:P 3 "register_operand"))
			(match_operand:VMODES 2 "register_operand"))
		       (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vle<VMODES:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")
	     (match_operand:VMODES 1 "memory_operand" "m")
	     (match_operand:VMODES 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vle.v\t%0,%1,%2.t" : "vle<sew>.v\t%0,%1,%2.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vse<VMODES:mode>_<P:mode>"
  [(parallel [(set (mem:VMODES (match_operand:P 1 "register_operand"))
		   (unspec:VMODES
		     [(match_operand:VMODES 0 "register_operand")
		      (mem:VMODES (match_dup 1))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vse<VMODES:mode>_nosetvl"
  [(set (match_operand:VMODES 1 "memory_operand" "+m")
	(unspec:VMODES
	  [(match_operand:VMODES 0 "register_operand" "vr")
	   (match_dup 1)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vse.v\t%0,%1" : "vse<sew>.v\t%0,%1"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vse<VMODES:mode>_<P:mode>_mask"
  [(parallel [(set (mem:VMODES (match_operand:P 2 "register_operand"))
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:<VCMPEQUIV> 0 "register_operand")
			 (match_operand:VMODES 1 "register_operand")
			 (mem:VMODES (match_dup 2))]
		       UNSPEC_MASKED_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vse<VMODES:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "memory_operand" "+m")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VMODES 2 "register_operand" "vr")
	      (match_dup 0)]
	    UNSPEC_MASKED_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vse.v\t%2,%0,%1.t" : "vse<sew>.v\t%2,%0,%1.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector Strided Instructions

(define_expand "vlse<VMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:P 1 "register_operand")
			 (match_operand:P 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_STRIDED_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vlse<VMODES:mode>_<P:mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:P 2 "register_operand" "r")
	      (mem:BLK (scratch))]
	    UNSPEC_STRIDED_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlse.v\t%0,(%1),%2" : "vlse<sew>.v\t%0,(%1),%2"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vlse<VMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
		      (match_operand:<VCMPEQUIV> 1 "register_operand")
		      (unspec:VMODES
			[(match_operand:P 3 "register_operand")
			 (match_operand:P 4 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_STRIDED_LOAD)
		      (match_operand:VMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vlse<VMODES:mode>_<P:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	     (unspec:VMODES
	       [(match_operand:P 1 "register_operand" "r")
		(match_operand:P 2 "register_operand" "r")
		(mem:BLK (scratch))]
	      UNSPEC_STRIDED_LOAD)
	     (match_operand:VMODES 4 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlse.v\t%0,(%1),%2,%3.t" : "vlse<sew>.v\t%0,(%1),%2,%3.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vsse<VMODES:mode>_<P:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
		        [(match_operand:VMODES 0 "register_operand")
			 (match_operand:P 1 "register_operand")
			 (match_operand:P 2 "register_operand")]
		       UNSPEC_STRIDED_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vsse<VMODES:mode>_<P:mode>_nosetvl"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:VMODES 0 "register_operand" "vr")
	      (match_operand:P 1 "register_operand" "r")
	      (match_operand:P 2 "register_operand" "r")]
	    UNSPEC_STRIDED_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsse.v\t%0,(%1),%2" : "vsse<sew>.v\t%0,(%1),%2"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vsse<VMODES:mode>_<P:mode>_mask"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:<VCMPEQUIV> 0 "register_operand")
			 (match_operand:VMODES 1 "register_operand")
			 (match_operand:P 2 "register_operand")
			 (match_operand:P 3 "register_operand")]
		       UNSPEC_STRIDED_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vsse<VMODES:mode>_<P:mode>_mask_nosetvl"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	      (match_operand:VMODES 0 "register_operand" "vr")
	      (match_operand:P 1 "register_operand" "r")
	      (match_operand:P 2 "register_operand" "r")]
	    UNSPEC_STRIDED_STORE)
	    (reg:SI VL_REGNUM)]
	  UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsse.v\t%0,(%1),%2,%3.t" : "vsse<sew>.v\t%0,(%1),%2,%3.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

;; Vector Indexed Load instructions

;; XXX: we should support gather load for XLEN is 32.
(define_expand "gather_load<VMODES:mode><VIMODES:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (match_operand 3 "const_0_operand")
			 (match_operand 4 "const_1_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_LOAD_GATHER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_64BIT && TARGET_VECTOR"
{
})

(define_insn "*gather_load<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:VIMODES 2 "register_operand" "vr")
	      (match_operand 3 "const_0_operand" "J")
	      (match_operand 4 "const_1_operand" "Wsa")
	      (mem:BLK (scratch))]
	    UNSPEC_LOAD_GATHER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlxe.v\t%0,(%1),%2" : "vloxei<VIMODES:sew>.v\t%0,(%1),%2"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "mask_gather_load<VMODES:mode><VIMODES:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:<VMODES:VCMPEQUIV> 5 "register_operand")
			 (match_operand 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (match_operand 3 "const_0_operand")
			 (match_operand 4 "const_1_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_LOAD_GATHER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_64BIT && TARGET_VECTOR"
{
})

(define_insn "*mask_gather_load<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:<VMODES:VCMPEQUIV> 5 "register_operand" "vm")
	       (match_operand:P 1 "register_operand" "r")
	       (match_operand:VIMODES 2 "register_operand" "vr")
	       (match_operand 3 "const_0_operand" "J")
	       (match_operand 4 "const_1_operand" "Wsa")
	       (mem:BLK (scratch))]
	    UNSPEC_LOAD_GATHER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlxe.v\t%0,(%1),%2,%5.t" : "vloxei<VIMODES:sew>.v\t%0,(%1),%2,%5.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

;; Vector Indexed Load instructions for builtin

(define_expand "vloxei<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:P 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_INDEXED_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vloxei<VMODES:mode><VIMODES:mode>_<P:mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:VIMODES 2 "register_operand" "vr")
	      (mem:BLK (scratch))]
	    UNSPEC_INDEXED_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlxe.v\t%0,(%1),%2" : "vloxei<VIMODES:sew>.v\t%0,(%1),%2"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vloxei<VMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:<VMODES:VCMPEQUIV> 1 "register_operand")
			 (match_operand:VMODES 2 "register_operand")
			 (match_operand:P 3 "register_operand")
			 (match_operand:VIMODES 4 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_INDEXED_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vloxei<VMODES:mode><VIMODES:mode>_<P:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:<VMODES:VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VMODES 2 "register_operand" "0")
	      (match_operand:P 3 "register_operand" "r")
	      (match_operand:VIMODES 4 "register_operand" "vr")
	      (mem:BLK (scratch))]
	    UNSPEC_INDEXED_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlxe.v\t%0,(%3),%4,%1.t" : "vloxei<VIMODES:sew>.v\t%0,(%3),%4,%1.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vluxei<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:P 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_UNORDERED_INDEXED_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vluxei<VMODES:mode><VIMODES:mode>_<P:mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:VIMODES 2 "register_operand" "vr")
	      (mem:BLK (scratch))]
	    UNSPEC_UNORDERED_INDEXED_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vluxei<VIMODES:sew>.v\t%0,(%1),%2"
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vluxei<VMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:<VMODES:VCMPEQUIV> 1 "register_operand")
			 (match_operand:VMODES 2 "register_operand")
			 (match_operand:P 3 "register_operand")
			 (match_operand:VIMODES 4 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_UNORDERED_INDEXED_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vluxei<VMODES:mode><VIMODES:mode>_<P:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:<VMODES:VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VMODES 2 "register_operand" "0")
	      (match_operand:P 3 "register_operand" "r")
	      (match_operand:VIMODES 4 "register_operand" "vr")
	      (mem:BLK (scratch))]
	    UNSPEC_UNORDERED_INDEXED_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vluxei<VIMODES:sew>.v\t%0,(%3),%4,%1.t"
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

;; Vector Indexed Store Instructions

;; XXX: we should support scatter store for XLEN is 32.
(define_expand "scatter_store<VMODES:mode><VIMODES:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand 0 "register_operand")
			 (match_operand:VIMODES 1 "register_operand")
			 (match_operand 2 "const_0_operand")
			 (match_operand 3 "const_1_operand")
			 (match_operand:VMODES 4 "register_operand")]
		       UNSPEC_STORE_SCATTER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_64BIT && TARGET_VECTOR"
{
})

(define_insn "*scatter_store<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:VIMODES 1 "register_operand" "vr")
	      (match_operand 2 "const_0_operand" "J")
	      (match_operand 3 "const_1_operand" "Wsa")
	      (match_operand:VMODES 4 "register_operand" "vr")]
	    UNSPEC_STORE_SCATTER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsxe.v\t%4,(%0),%1" : "vsoxei<VIMODES:sew>.v\t%4,(%0),%1"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "mask_scatter_store<VMODES:mode><VIMODES:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:<VMODES:VCMPEQUIV> 5 "register_operand")
			 (match_operand 0 "register_operand")
			 (match_operand:VIMODES 1 "register_operand")
			 (match_operand 2 "const_0_operand")
			 (match_operand 3 "const_1_operand")
			 (match_operand:VMODES 4 "register_operand")]
		       UNSPEC_STORE_SCATTER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_64BIT && TARGET_VECTOR"
{
})

(define_insn "*mask_scatter_store<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:<VMODES:VCMPEQUIV> 5 "register_operand" "vm")
	      (match_operand:P 0 "register_operand" "r")
	      (match_operand:VIMODES 1 "register_operand" "vr")
	      (match_operand 2 "const_0_operand" "J")
	      (match_operand 3 "const_1_operand" "Wsa")
	      (match_operand:VMODES 4 "register_operand" "vr")]
	    UNSPEC_STORE_SCATTER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsxe.v\t%4,(%0),%1,%5.t" : "vsoxei<VIMODES:sew>.v\t%4,(%0),%1,%5.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

;; Vector Ordered-Indexed Store instructions for builtin

(define_expand "vs<order>xei<VMODES:mode><VIMODES:mode>_<P:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 0 "register_operand")
			 (match_operand:VIMODES 1 "register_operand")
			 (match_operand:VMODES 2 "register_operand")]
		       UNSPEC_INDEXED_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vs<order>xei<VMODES:mode><VIMODES:mode>_<P:mode>_nosetvl"
   [(set (mem:BLK (scratch))
	 (unspec:BLK
	   [(unspec:BLK
	      [(match_operand:P 0 "register_operand" "r")
	       (match_operand:VIMODES 1 "register_operand" "vr")
	       (match_operand:VMODES 2 "register_operand" "vr")]
	     UNSPEC_INDEXED_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? (!strcmp ("<order>", "o") ? "vsxe.v\t%2,(%0),%1" : "vs<order>xe.v\t%2,(%0),%1")
    : "vs<order>xei<VIMODES:sew>.v\t%2,(%0),%1"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vs<order>xei<VMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:<VMODES:VCMPEQUIV> 0 "register_operand")
			 (match_operand:P 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (match_operand:VMODES 3 "register_operand")]
		       UNSPEC_INDEXED_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vs<order>xei<VMODES:mode><VIMODES:mode>_<P:mode>_mask_nosetvl"
   [(set (mem:BLK (scratch))
	 (unspec:BLK
	   [(unspec:BLK
	      [(match_operand:<VMODES:VCMPEQUIV> 0 "register_operand" "vm")
	       (match_operand:P 1 "register_operand" "r")
	       (match_operand:VIMODES 2 "register_operand" "vr")
	       (match_operand:VMODES 3 "register_operand" "vr")]
	     UNSPEC_INDEXED_STORE)
	   (reg:SI VL_REGNUM)]
	  UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? (!strcmp ("<order>", "o") ? "vsxe.v\t%3,(%1),%2,%0.t" : "vs<order>xe.v\t%3,(%1),%2,%0.t")
    : "vs<order>xei<VIMODES:sew>.v\t%3,(%1),%2,%0.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

;; Move instructions.

;; ??? We need the m constraints here only if we want load/store to work
;; directly without using an intrinsic.

;; If operand 1 is a const_vector, then we can't split until after reload,
;; to ensure that the scratch operand has been allocated a reg first.
(define_expand "mov<mode>"
  [(set (match_operand:VMODES 0 "reg_or_mem_operand")
		(match_operand:VMODES 1 "vector_move_operand"))]
  "TARGET_VECTOR"
{
  /* Need to force register if mem <- !reg.  */
  if (MEM_P (operands[0]) && !REG_P (operands[1]))
    operands[1] = force_reg (<MODE>mode, operands[1]);

  rtx dup_value;

  /* If we have a const vector, then we have to load it's value into the
     scratch reg, and then create a vec_duplicate of it.  */
  if (const_vec_duplicate_p (operands[1], &dup_value))
    {
      gcc_assert (can_create_pseudo_p ());
      rtx tmp_reg = gen_reg_rtx (<VSUBMODE>mode);
      emit_move_insn (tmp_reg, dup_value);
      if (TARGET_64BIT)
	emit_insn (gen_vsetvlmax<vlmode>_di (gen_reg_rtx (Pmode)));
      else
	emit_insn (gen_vsetvlmax<vlmode>_si (gen_reg_rtx (Pmode)));
      emit_insn (gen_vec_duplicate<mode>_nosetvl (operands[0], tmp_reg));
      DONE;
    }

  if (target_subset_version_p ("v", 0, 7))
    {
      emit_insn (gen_mov<mode>_v0p7 (operands[0], operands[1]));
      DONE;
    }
})

(define_insn "*mov<mode>"
  [(set (match_operand:VMODES 0 "reg_or_mem_operand"  "=vr,vr,m")
	    (match_operand:VMODES 1 "reg_or_mem_operand"  "vr,m,vr"))]
  "TARGET_VECTOR && !target_subset_version_p (\"v\", 0, 7)"
  "@
   vmv<xlmul>r.v\t%0,%1
   vl<xlmul>r.v\t%0,%1
   vs<xlmul>r.v\t%1,%0"
  [(set_attr "type" "vmove,vload,vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mov<mode>"
  [(set (match_operand:VMASKMODES 0 "reg_or_mem_operand")
		(match_operand:VMASKMODES 1 "vector_move_operand"))]
  "TARGET_VECTOR"
{
  rtx ele;
  if (const_vec_duplicate_p (operands[1], &ele))
    {
      gcc_assert (CONST_INT_P (ele));
      switch (INTVAL (ele))
	{
	case 0:
	  emit_insn (gen_clr<mode> (operands[0]));
	  break;
	case 1:
	  emit_insn (gen_set<mode> (operands[0]));
	  break;
	default:
	  gcc_unreachable ();
	}
      DONE;
    }

  if (target_subset_version_p ("v", 0, 7))
    {
      emit_insn (gen_mov<mode>_v0p7 (operands[0], operands[1]));
      DONE;
    }
})

;; move pattern for vector masking type.
(define_insn "*mov<mode>"
  [(set (match_operand:VMASKMODES 0 "reg_or_mem_operand"  "=vr,vr, m")
	    (match_operand:VMASKMODES 1 "reg_or_mem_operand"  " vr, m, vr"))]
  "TARGET_VECTOR && !target_subset_version_p (\"v\", 0, 7)"
  "@
   vmv1r.v\t%0, %1
   vl1r.v\t%0, %1
   vs1r.v\t%1, %0"
  [(set_attr "type" "vmove,vload,vstore")
   (set_attr "mode" "none")])

;; Integer Scalar Move Instructions.

;; XXX: we should support scalar move for XLEN is 32.
(define_expand "vec_extract<mode>"
  [(parallel [(set (match_operand:<VSUBMODE> 0 "register_operand")
		   (unspec:<VSUBMODE>
		     [(vec_select:<VSUBMODE>
			(match_operand:VIMODES 1 "register_operand")
			(parallel [(const_int 0)]))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "vec_extract<mode>_nosetvl"
  [(set (match_operand:<VSUBMODE> 0 "register_operand" "=r")
	(unspec:<VSUBMODE>
	  [(vec_select:<VSUBMODE>
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (parallel [(const_int 0)]))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmv.x.s\t%0,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_set<mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(vec_merge:VIMODES
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VIMODES 1 "register_operand")
			(const_int 1))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "vec_set<mode>_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(vec_merge:VIMODES
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "0")
	     (const_int 1))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmv.s.x\t%0,%2"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP Scalar Move Instructions.

;; XXX: we should support scalar move for FLEN is 32.
(define_expand "vec_extract_fext<mode>"
  [(parallel [(set (match_operand:<VSUBMODE> 0 "register_operand")
		   (unspec:<VSUBMODE>
		     [(vec_select:<VSUBMODE>
			(match_operand:VFMODES 1 "register_operand")
			(parallel [(const_int 0)]))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "vec_extract_fext<mode>_nosetvl"
  [(set (match_operand:<VSUBMODE> 0 "register_operand" "=f")
	(unspec:<VSUBMODE>
	  [(vec_select:<VSUBMODE>
	     (match_operand:VFMODES 1 "register_operand" "vr")
	     (parallel [(const_int 0)]))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfmv.f.s\t%0,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_set<mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(vec_merge:VFMODES
			(vec_duplicate:VFMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VFMODES 1 "register_operand")
			(const_int 1))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "vec_set<mode>_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(vec_merge:VFMODES
	     (vec_duplicate:VFMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "f"))
	     (match_operand:VFMODES 1 "register_operand" "0")
	     (const_int 1))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfmv.s.f\t%0,%2"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Single-Width Integer Add and Saturating Add

(define_expand "<optab><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(all_plus:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "vector_arith_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(all_plus:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr,vr")
	     (match_operand:VIMODES 2 "vector_arith_operand" "vr,vi"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%1,%2
   v<insn>.vi\t%0,%1,%v2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(all_plus:VIMODES
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(all_plus:VIMODES
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(all_plus:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "vector_arith_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (all_plus:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr,vr")
	       (match_operand:VIMODES 4 "vector_arith_operand" "vr,vi"))
	     (match_operand:VIMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%3,%4,%1.t
   v<insn>.vi\t%0,%3,%v4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(all_plus:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand"))
			  (match_operand:VIMODES 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (all_plus:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r"))
	       (match_operand:VIMODES 3 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Single-Width Integer Subtract and Saturating Subtract

(define_expand "<optab><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(sub_and_ssub:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "neg_vector_arith_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(sub_and_ssub:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr,vr")
	     (match_operand:VIMODES 2 "neg_vector_arith_operand" "vr,vj"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%1,%2
   v<neg_add>.vi\t%0,%1,%V2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "ussub<mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(us_minus:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*ussub<mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(us_minus:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (match_operand:VIMODES 2 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vssubu.vv\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; ??? No constant immediate subtract, should be converted to an add, but
;; this is not happening.  Maybe add it to this pattern?

(define_expand "<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(all_minus:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(all_minus:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(all_minus:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (all_minus:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (match_operand:VIMODES 4 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(all_minus:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (all_minus:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rsub<mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(minus:VIMODES
			(match_operand:VIMODES 2 "const_vector_int_operand")
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*rsub<mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(minus:VIMODES
	     (match_operand:VIMODES 2 "const_vector_int_operand" "vi")
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrsub.vi\t%0,%1,%v2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rsub<mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(minus:VIMODES
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*rsub<mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(minus:VIMODES
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrsub.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rsub<mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(minus:VIMODES
			  (match_operand:VIMODES 3 "const_vector_int_operand")
			  (match_operand:VIMODES 4 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*rsub<mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (minus:VIMODES
	       (match_operand:VIMODES 3 "const_vector_int_operand" "vi")
	       (match_operand:VIMODES 4 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrsub.vi\t%0,%4,%v3,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rsub<mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
		        (match_operand:<VCMPEQUIV> 1 "register_operand")
			(minus:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand"))
			  (match_operand:VIMODES 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*rsub<mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (minus:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r"))
	       (match_operand:VIMODES 3 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrsub.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Logical instructions

(define_expand "<optab><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_bitwise:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "vector_arith_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(any_bitwise:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr,vr")
	     (match_operand:VIMODES 2 "vector_arith_operand" "vr,vi"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%1,%2
   v<insn>.vi\t%0,%1,%v2"
  [(set_attr "type" "vlogical,vlogical")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_bitwise:VIMODES
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(any_bitwise:VIMODES
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%1,%2"
  [(set_attr "type" "vlogical")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_bitwise:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "vector_arith_operand"))
			(match_operand:VIMODES 2 "vector_arith_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (any_bitwise:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr,vr")
	       (match_operand:VIMODES 4 "vector_arith_operand" "vr,vi"))
	     (match_operand:VIMODES 2 "vector_arith_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%3,%4,%1.t
   v<insn>.vi\t%0,%3,%v4,%1.t"
  [(set_attr "type" "vlogical")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_bitwise:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_bitwise:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vlogical")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "one_cmpl<mode>2"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(not:VIMODES
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*one_cmpl<mode>2_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(not:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vnot.v\t%0,%1"
  [(set_attr "type" "vlogical")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "one_cmpl<mode>2_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(not:VIMODES
			  (match_operand:VIMODES 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*one_cmpl<mode>2_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (not:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vnot.v\t%0,%3,%1.t"
  [(set_attr "type" "vlogical")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "neg<mode>2"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(neg:VIMODES
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*neg<mode>2_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(neg:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vrsub.vx\t%0,%1,x0" : "vneg.v\t%0,%1"; }
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "neg<mode>2_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(neg:VIMODES
			  (match_operand:VIMODES 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*neg<mode>2_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (neg:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vrsub.vx\t%0,%3,x0,%1.t" : "vneg.v\t%0,%3,%1.t"; }
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector Integer Add-with-Carry / Subtract-with-Borrow Instructions

(define_expand "adc<mode>4"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(plus:VIMODES
			(plus:VIMODES
			  (match_operand:VIMODES 1 "register_operand")
			  (match_operand:VIMODES 2 "vector_arith_operand"))
		      (if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 3 "register_operand")
			(vec_duplicate:VIMODES (const_int 1))
			(vec_duplicate:VIMODES (const_int 0))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*adc<mode>4_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VIMODES
	  [(plus:VIMODES
	     (plus:VIMODES
	       (match_operand:VIMODES 1 "register_operand" "vr,vr")
	       (match_operand:VIMODES 2 "vector_arith_operand" "vr,vi"))
	     (if_then_else:VIMODES
	       (match_operand:<VCMPEQUIV> 3 "register_operand" "vm,vm")
	       (vec_duplicate:VIMODES (const_int 1))
	       (vec_duplicate:VIMODES (const_int 0))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vadc.vvm\t%0,%1,%2,%3
   vadc.vim\t%0,%1,%v2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "adc<mode>4_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(plus:VIMODES
			(plus:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))
			  (match_operand:VIMODES 1 "register_operand"))
			(if_then_else:VIMODES
			  (match_operand:<VCMPEQUIV> 3 "register_operand")
			  (vec_duplicate:VIMODES (const_int 1))
			  (vec_duplicate:VIMODES (const_int 0))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*adc<mode>4_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(plus:VIMODES
	     (plus:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	       (match_operand:VIMODES 1 "register_operand" "vr"))
	     (if_then_else:VIMODES
	       (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	       (vec_duplicate:VIMODES (const_int 1))
	       (vec_duplicate:VIMODES (const_int 0))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vadc.vxm\t%0,%1,%2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "madc<mode>4m"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(plus:VIMODES
			   (plus:VIMODES
			     (match_operand:VIMODES 1 "register_operand")
			     (match_operand:VIMODES 2 "vector_arith_operand"))
			   (if_then_else:VIMODES
			     (match_operand:<VCMPEQUIV> 3 "register_operand")
			     (vec_duplicate:VIMODES (const_int 1))
			     (vec_duplicate:VIMODES (const_int 0))))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*madc<mode>4m_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr,&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(plus:VIMODES
		(plus:VIMODES
		  (match_operand:VIMODES 1 "register_operand" "vr,vr")
		  (match_operand:VIMODES 2 "vector_arith_operand" "vr,vi"))
		(if_then_else:VIMODES
		  (match_operand:<VCMPEQUIV> 3 "register_operand" "vm,vm")
		  (vec_duplicate:VIMODES (const_int 1))
		  (vec_duplicate:VIMODES (const_int 0))))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vmadc.vvm\t%0,%1,%2,%3
   vmadc.vim\t%0,%1,%v2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "madc<mode>4m_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(plus:VIMODES
			   (plus:VIMODES
			     (vec_duplicate:VIMODES
			       (match_operand:<VSUBMODE> 2 "register_operand"))
			     (match_operand:VIMODES 1 "register_operand"))
			   (if_then_else:VIMODES
			     (match_operand:<VCMPEQUIV> 3 "register_operand")
			     (vec_duplicate:VIMODES (const_int 1))
			     (vec_duplicate:VIMODES (const_int 0))))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*madc<mode>4m_scalar_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(plus:VIMODES
		(plus:VIMODES
		  (vec_duplicate:VIMODES
		    (match_operand:<VSUBMODE> 2 "register_operand" "r"))
		   (match_operand:VIMODES 1 "register_operand" "vr"))
		(if_then_else:VIMODES
		  (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
		  (vec_duplicate:VIMODES (const_int 1))
		  (vec_duplicate:VIMODES (const_int 0))))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmadc.vxm\t%0,%1,%2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "madc<mode>4"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(plus:VIMODES
			   (match_operand:VIMODES 1 "register_operand")
			   (match_operand:VIMODES 2 "vector_arith_operand"))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*madc<mode>4_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr,&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(plus:VIMODES
		(match_operand:VIMODES 1 "register_operand" "vr,vr")
		(match_operand:VIMODES 2 "vector_arith_operand" "vr,vi"))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vmadc.vv\t%0,%1,%2
   vmadc.vi\t%0,%1,%v2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "madc<mode>4_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(plus:VIMODES
			   (vec_duplicate:VIMODES
			     (match_operand:<VSUBMODE> 2 "register_operand"))
			   (match_operand:VIMODES 1 "register_operand"))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*madc<mode>4_scalar_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(plus:VIMODES
	        (vec_duplicate:VIMODES
		  (match_operand:<VSUBMODE> 2 "register_operand" "r"))
		(match_operand:VIMODES 1 "register_operand" "vr"))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmadc.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sbc<mode>4"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(minus:VIMODES
			(minus:VIMODES
			  (match_operand:VIMODES 1 "register_operand")
			  (match_operand:VIMODES 2 "vector_arith_operand"))
			(if_then_else:VIMODES
			  (match_operand:<VCMPEQUIV> 3 "register_operand")
			  (vec_duplicate:VIMODES (const_int 1))
			  (vec_duplicate:VIMODES (const_int 0))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sbc<mode>4_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(minus:VIMODES
	     (minus:VIMODES
	       (match_operand:VIMODES 1 "register_operand" "vr")
	       (match_operand:VIMODES 2 "register_operand" "vr"))
	     (if_then_else:VIMODES
	       (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	       (vec_duplicate:VIMODES (const_int 1))
	       (vec_duplicate:VIMODES (const_int 0))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vsbc.vvm\t%0,%1,%2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sbc<mode>4_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(minus:VIMODES
			(minus:VIMODES
			  (match_operand:VIMODES 1 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand")))
			(if_then_else:VIMODES
			  (match_operand:<VCMPEQUIV> 3 "register_operand")
			  (vec_duplicate:VIMODES (const_int 1))
			  (vec_duplicate:VIMODES (const_int 0))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sbc<mode>4_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(minus:VIMODES
	     (minus:VIMODES
	       (match_operand:VIMODES 1 "register_operand" "vr")
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r")))
	     (if_then_else:VIMODES
	       (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	       (vec_duplicate:VIMODES (const_int 1))
	       (vec_duplicate:VIMODES (const_int 0))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vsbc.vxm\t%0,%1,%2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "msbc<mode>4m"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(minus:VIMODES
			   (minus:VIMODES
			     (match_operand:VIMODES 1 "register_operand")
			     (match_operand:VIMODES 2 "vector_arith_operand"))
			   (if_then_else:VIMODES
			     (match_operand:<VCMPEQUIV> 3 "register_operand")
			     (vec_duplicate:VIMODES (const_int 1))
			     (vec_duplicate:VIMODES (const_int 0))))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*msbc<mode>4m_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(minus:VIMODES
		(minus:VIMODES
		  (match_operand:VIMODES 1 "register_operand" "vr")
		  (match_operand:VIMODES 2 "register_operand" "vr"))
		(if_then_else:VIMODES
		  (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
		  (vec_duplicate:VIMODES (const_int 1))
		  (vec_duplicate:VIMODES (const_int 0))))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmsbc.vvm\t%0,%1,%2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "msbc<mode>4m_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(minus:VIMODES
			   (minus:VIMODES
			     (match_operand:VIMODES 1 "register_operand")
			     (vec_duplicate:VIMODES
			       (match_operand:<VSUBMODE> 2 "register_operand")))
			   (if_then_else:VIMODES
			     (match_operand:<VCMPEQUIV> 3 "register_operand")
			     (vec_duplicate:VIMODES (const_int 1))
			     (vec_duplicate:VIMODES (const_int 0))))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*msbc<mode>4m_scalar_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(minus:VIMODES
		(minus:VIMODES
		  (match_operand:VIMODES 1 "register_operand" "vr")
		  (vec_duplicate:VIMODES
		    (match_operand:<VSUBMODE> 2 "register_operand" "r")))
		(if_then_else:VIMODES
		  (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
		  (vec_duplicate:VIMODES (const_int 1))
		  (vec_duplicate:VIMODES (const_int 0))))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmsbc.vxm\t%0,%1,%2,%3"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "msbc<mode>4"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(minus:VIMODES
			   (match_operand:VIMODES 1 "register_operand")
			   (match_operand:VIMODES 2 "register_operand"))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*madc<mode>4_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(minus:VIMODES
		(match_operand:VIMODES 1 "register_operand" "vr")
		(match_operand:VIMODES 2 "register_operand" "vr"))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmsbc.vv\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "msbc<mode>4_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(unspec:<VCMPEQUIV>
			[(minus:VIMODES
			   (match_operand:VIMODES 1 "register_operand")
			   (vec_duplicate:VIMODES
			     (match_operand:<VSUBMODE> 2 "register_operand")))]
		       UNSPEC_OVERFLOW)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*msbc<mode>4_scalar_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(unspec:<VCMPEQUIV>
	     [(minus:VIMODES
		(match_operand:VIMODES 1 "register_operand" "vr")
		(vec_duplicate:VIMODES
		  (match_operand:<VSUBMODE> 2 "register_operand" "r")))]
	    UNSPEC_OVERFLOW)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmsbc.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])


;; Floating point ALU instructions (add/sub/rsub/mul/div/rdiv).

(define_expand "<optab><mode>3"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(any_fop:VFMODES
			(match_operand:VFMODES 1 "register_operand")
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<optab><mode>3_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(any_fop:VFMODES
	     (match_operand:VFMODES 1 "register_operand" "vr")
	     (match_operand:VFMODES 2 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<optab>.vv\t%0,%1,%2"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fop:VFMODES
			  (match_operand:VFMODES 3 "register_operand")
			  (match_operand:VFMODES 4 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<optab><mode>3_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fop:VFMODES
	       (match_operand:VFMODES 3 "register_operand" "vr")
	       (match_operand:VFMODES 4 "register_operand" "vr"))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<optab>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(any_fcomop:VFMODES
			(vec_duplicate:VFMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(any_fcomop:VFMODES
	     (vec_duplicate:VFMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "f"))
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<optab>.vf\t%0,%1,%2"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fcomop:VFMODES
			  (vec_duplicate:VFMODES
			    (match_operand:<VSUBMODE> 4 "register_operand"))
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fcomop:VFMODES
	       (vec_duplicate:VFMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "f"))
	       (match_operand:VFMODES 3 "register_operand" "vr"))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<optab>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(any_fnoncomop:VFMODES
		        (match_operand:VFMODES 1 "register_operand")
			(vec_duplicate:VFMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(any_fnoncomop:VFMODES
	     (match_operand:VFMODES 1 "register_operand" "vr")
	     (vec_duplicate:VFMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "f")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<optab>.vf\t%0,%1,%2"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fnoncomop:VFMODES
			  (match_operand:VFMODES 3 "register_operand")
			  (vec_duplicate:VFMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fnoncomop:VFMODES
	       (match_operand:VFMODES 3 "register_operand" "vr")
	       (vec_duplicate:VFMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "f")))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<optab>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "r<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(any_fnoncomop:VFMODES
			(vec_duplicate:VFMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*r<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(any_fnoncomop:VFMODES
	     (vec_duplicate:VFMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "f"))
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfr<optab>.vf\t%0,%1,%2"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "r<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fnoncomop:VFMODES
			  (vec_duplicate:VFMODES
			    (match_operand:<VSUBMODE> 4 "register_operand"))
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*r<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fnoncomop:VFMODES
	       (vec_duplicate:VFMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "f"))
	       (match_operand:VFMODES 3 "register_operand" "vr"))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfr<optab>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "<any_fop_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector Floating-Point Square-Root Instruction
(define_expand "sqrt<mode>2"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(sqrt:VFMODES
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*sqrt<mode>2_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(sqrt:VFMODES
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfsqrt.v\t%0,%1"
  [(set_attr "type" "vfsqrt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sqrt<mode>2_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(sqrt:VFMODES
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*sqrt<mode>2_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (sqrt:VFMODES
	       (match_operand:VFMODES 3 "register_operand" "vr"))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfsqrt.v\t%0,%3,%1.t"
  [(set_attr "type" "vfsqrt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rsqrt7<mode>2"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(match_operand:VFMODES 1 "register_operand")
		      (reg:SI VL_REGNUM)]
		    UNSPEC_RSQRTE7))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*rsqrt7<mode>2_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(match_operand:VFMODES 1 "register_operand" "vr")
	   (reg:SI VL_REGNUM)]
	 UNSPEC_RSQRTE7))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfrsqrt7.v\t%0,%1"
  [(set_attr "type" "vfsqrt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rsqrt7<mode>2_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(match_operand:<VCMPEQUIV> 1 "register_operand")
		      (match_operand:VFMODES 3 "register_operand")
		      (match_operand:VFMODES 2 "register_operand")
		      (reg:SI VL_REGNUM)]
		    UNSPEC_RSQRTE7))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*rsqrt7<mode>2_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	   (match_operand:VFMODES 3 "register_operand" "vr")
	   (match_operand:VFMODES 2 "register_operand" "0")
	   (reg:SI VL_REGNUM)]
	 UNSPEC_RSQRTE7))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfrsqrt7.v\t%0,%3,%1.t"
  [(set_attr "type" "vfsqrt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "neg<mode>2"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(neg:VFMODES
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_expand "rec7<mode>2"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(match_operand:VFMODES 1 "register_operand")
		      (reg:SI VL_REGNUM)]
		    UNSPEC_RECE7))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*rec7<mode>2_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(match_operand:VFMODES 1 "register_operand" "vr")
	   (reg:SI VL_REGNUM)]
	 UNSPEC_RECE7))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfrec7.v\t%0,%1"
  [(set_attr "type" "vfrec")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "rec7<mode>2_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(match_operand:<VCMPEQUIV> 1 "register_operand")
		      (match_operand:VFMODES 3 "register_operand")
		      (match_operand:VFMODES 2 "register_operand")
		      (reg:SI VL_REGNUM)]
		    UNSPEC_RECE7))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*rec7<mode>2_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	   (match_operand:VFMODES 3 "register_operand" "vr")
	   (match_operand:VFMODES 2 "register_operand" "0")
	   (reg:SI VL_REGNUM)]
	 UNSPEC_RECE7))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfrec7.v\t%0,%3,%1.t"
  [(set_attr "type" "vfrec")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])


(define_insn "*neg<mode>2_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(neg:VFMODES
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfsgnjn.vv\t%0,%1,%1" : "vfneg.v\t%0,%1"; }
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "neg<mode>2_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(neg:VFMODES
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*neg<mode>2_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (neg:VFMODES
	       (match_operand:VFMODES 3 "register_operand" "vr"))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfsgnjn.vv\t%0,%3,%3,%1.t" : "vfneg.v\t%0,%3,%1.t"; }
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])


;; Vector Widening Integer Add/Subtract

(define_expand "w<add_sub:optab><any_extend:u><mode>_vv"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 1 "register_operand"))
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_vv_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 1 "register_operand" "vr"))
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.vv\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_vv_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 1 "register_operand"))
			(any_extend:<VWMODE>
			  (vec_duplicate:VWIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_vv_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 1 "register_operand" "vr"))
	     (any_extend:<VWMODE>
	       (vec_duplicate:VWIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_vv_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand"))
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_vv_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 3 "register_operand" "vr"))
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_vv_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand"))
			  (any_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_vv_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 3 "register_operand" "vr"))
	       (any_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_wv"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(match_operand:<VWMODE> 1 "register_operand")
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_wv_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (match_operand:<VWMODE> 1 "register_operand" "vr")
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.wv\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_wv_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(match_operand:<VWMODE> 1 "register_operand")
			(any_extend:<VWMODE>
			  (vec_duplicate:VWIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_wv_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (match_operand:<VWMODE> 1 "register_operand" "vr")
	     (any_extend:<VWMODE>
	       (vec_duplicate:VWIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.wx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_wv_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (match_operand:<VWMODE> 3 "register_operand")
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_wv_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (match_operand:<VWMODE> 3 "register_operand" "vr")
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.wv\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "w<add_sub:optab><any_extend:u><mode>_wv_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (match_operand:<VWMODE> 3 "register_operand")
			  (any_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*w<add_sub:optab><any_extend:u><mode>_wv_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (match_operand:<VWMODE> 3 "register_operand" "vr")
	       (any_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vw<add_sub:insn><any_extend:u>.wx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Widening Sign-extend and Zero-extend

(define_expand "wcvt<u><mode><vwmode>2"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(any_extend:<VWMODE>
			(match_operand:VWIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wcvt<u><mode><vwmode>2_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(any_extend:<VWMODE>
	     (match_operand:VWIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwcvt<u>.x.x.v\t%0,%1"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wcvt<u><mode><vwmode>2_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 3 "register_operand"))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wcvt<u><mode><vwmode>2_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 3 "register_operand" "vr"))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwcvt<u>.x.x.v\t%0,%3,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sz_op>extend<mode><vwmode>2"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(any_extend:<VWMODE>
			(match_operand:VWIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VWVLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sz_op>extend<mode><vwmode>2_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(any_extend:<VWMODE>
	     (match_operand:VWIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VWVLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<sz>ext.vf2\t%0,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sz_op>extend<mode><vwmode>2_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 3 "register_operand"))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VWVLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sz_op>extend<mode><vwmode>2_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 3 "register_operand" "vr"))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VWVLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<sz>ext.vf2\t%0,%3,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Quad-Widening Sign-extend and Zero-extend

(define_expand "<sz_op>extend<mode><vqwmode>2"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(any_extend:<VQWMODE>
			(match_operand:VQWIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VQWVLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sz_op>extend<mode><vqwmode>2_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(any_extend:<VQWMODE>
	     (match_operand:VQWIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VQWVLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<sz>ext.vf4\t%0,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sz_op>extend<mode><vqwmode>2_mask"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(if_then_else:<VQWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_extend:<VQWMODE>
			  (match_operand:VQWIMODES 3 "register_operand"))
			(match_operand:<VQWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VQWVLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sz_op>extend<mode><vqwmode>2_mask_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(if_then_else:<VQWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_extend:<VQWMODE>
	       (match_operand:VQWIMODES 3 "register_operand" "vr"))
	     (match_operand:<VQWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VQWVLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<sz>ext.vf4\t%0,%3,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Eighth Sign-extend and Zero-extend

(define_expand "<sz_op>extend<mode><vewmode>2"
  [(parallel [(set (match_operand:<VEWMODE> 0 "register_operand")
		   (unspec:<VEWMODE>
		     [(any_extend:<VEWMODE>
			(match_operand:VEWIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VEWVLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sz_op>extend<mode><vewmode>2_nosetvl"
  [(set (match_operand:<VEWMODE> 0 "register_operand" "=&vr")
	(unspec:<VEWMODE>
	  [(any_extend:<VEWMODE>
	     (match_operand:VEWIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VEWVLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<sz>ext.vf8\t%0,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "DI")])

(define_expand "<sz_op>extend<mode><vewmode>2_mask"
  [(parallel [(set (match_operand:<VEWMODE> 0 "register_operand")
		   (unspec:<VEWMODE>
		     [(if_then_else:<VEWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_extend:<VEWMODE>
			  (match_operand:VEWIMODES 3 "register_operand"))
			(match_operand:<VEWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VEWVLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sz_op>extend<mode><vewmode>2_mask_nosetvl"
  [(set (match_operand:<VEWMODE> 0 "register_operand" "=&vr")
	(unspec:<VEWMODE>
	  [(if_then_else:<VEWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_extend:<VEWMODE>
	       (match_operand:VEWIMODES 3 "register_operand" "vr"))
	     (match_operand:<VEWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VEWVLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<sz>ext.vf8\t%0,%3,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "DI")])

;; Vector Widening Floating-Point Add/Subtract

(define_expand "fw<optab><mode>_vv"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 1 "register_operand"))
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_vv_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 1 "register_operand" "vr"))
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.vv\t%0,%1,%2"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_vv_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 1 "register_operand"))
			(float_extend:<VWMODE>
			  (vec_duplicate:VWFMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_vv_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 1 "register_operand" "vr"))
	     (float_extend:<VWMODE>
	       (vec_duplicate:VWFMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "f"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.vf\t%0,%1,%2"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_vv_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 3 "register_operand"))
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_vv_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 3 "register_operand" "vr"))
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_vv_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 3 "register_operand"))
			  (float_extend:<VWMODE>
			    (vec_duplicate:VWFMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_vv_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 3 "register_operand" "vr"))
	       (float_extend:<VWMODE>
		 (vec_duplicate:VWFMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "f"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_wv"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(match_operand:<VWMODE> 1 "register_operand")
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_wv_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (match_operand:<VWMODE> 1 "register_operand" "vr")
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.wv\t%0,%1,%2"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_wv_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(match_operand:<VWMODE> 1 "register_operand")
			(float_extend:<VWMODE>
			  (vec_duplicate:VWFMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_wv_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (match_operand:<VWMODE> 1 "register_operand" "vr")
	     (float_extend:<VWMODE>
	       (vec_duplicate:VWFMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "f"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.wf\t%0,%1,%2"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_wv_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (match_operand:<VWMODE> 3 "register_operand")
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_wv_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (match_operand:<VWMODE> 3 "register_operand" "vr")
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.wv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fw<optab><mode>_wv_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(add_sub:<VWMODE>
			  (match_operand:<VWMODE> 3 "register_operand")
			  (float_extend:<VWMODE>
			    (vec_duplicate:VWFMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fw<optab><mode>_wv_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (add_sub:<VWMODE>
	       (match_operand:<VWMODE> 3 "register_operand" "vr")
	       (float_extend:<VWMODE>
		 (vec_duplicate:VWFMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "f"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfw<insn>.wf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Integer multiply instructions.

(define_expand "mul<mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(mult:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*mul<mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(mult:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (match_operand:VIMODES 2 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmul.vv\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mul<mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(mult:VIMODES
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*mul<mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(mult:VIMODES
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmul.vx\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mul<mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*mul<mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (match_operand:VIMODES 4 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmul.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mul<mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand"))
			  (match_operand:VIMODES 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*mul<mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r"))
		 (match_operand:VIMODES 3 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmul.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Multiply with vector-vector and returning high bits of product

(define_expand "<su>mul<mode>3_highpart"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(truncate:VIMODES
			(ashiftrt:<EXT_VIMODES>
			  (mult:<EXT_VIMODES>
			    (any_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 1 "register_operand"))
			    (any_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 2 "register_operand")))
			  (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<su>mul<mode>3_highpart_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(truncate:VIMODES
	     (ashiftrt:<EXT_VIMODES>
	       (mult:<EXT_VIMODES>
		 (any_extend:<EXT_VIMODES>
		   (match_operand:VIMODES 1 "register_operand" "vr"))
		 (any_extend:<EXT_VIMODES>
		   (match_operand:VIMODES 2 "register_operand" "vr")))
	       (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand" "Ws<vmsize>")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulh<u>.vv\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumul<mode>3_highpart"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(truncate:VIMODES
			(ashiftrt:<EXT_VIMODES>
			  (mult:<EXT_VIMODES>
			    (sign_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 1 "register_operand"))
			    (zero_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 2 "register_operand")))
			  (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumul<mode>3_highpart_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(truncate:VIMODES
	     (ashiftrt:<EXT_VIMODES>
	       (mult:<EXT_VIMODES>
		 (sign_extend:<EXT_VIMODES>
		   (match_operand:VIMODES 1 "register_operand" "vr"))
		 (zero_extend:<EXT_VIMODES>
		   (match_operand:VIMODES 2 "register_operand" "vr")))
	       (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand" "Ws<vmsize>")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulhsu.vv\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mulh<v_su><mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")]
		       UNSPEC_VMULH)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
  rtx shift = GEN_INT (GET_MODE_UNIT_BITSIZE (<VIMODES:MODE>mode));
  emit_insn (gen_<v_su>mul<mode>3_highpart (operands[0], operands[1],
					    operands[2], shift));
  DONE;
})

;;Multiply with vector-scalar returning high bits of product

(define_expand "<su>mul<mode>3_highpart_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(truncate:VIMODES
			(ashiftrt:<EXT_VIMODES>
			  (mult:<EXT_VIMODES>
			    (any_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 1 "register_operand"))
			    (any_extend:<EXT_VIMODES>
			      (vec_duplicate:VIMODES
				(match_operand:<VSUBMODE> 2 "register_operand"))))
			  (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<su>mul<mode>3_highpart_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(truncate:VIMODES
	     (ashiftrt:<EXT_VIMODES>
	       (mult:<EXT_VIMODES>
		 (any_extend:<EXT_VIMODES>
		   (match_operand:VIMODES 1 "register_operand" "vr"))
		 (any_extend:<EXT_VIMODES>
		   (vec_duplicate:VIMODES
		     (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	       (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand" "Ws<vmsize>")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulh<u>.vx\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumul<mode>3_highpart_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(truncate:VIMODES
			(ashiftrt:<EXT_VIMODES>
			  (mult:<EXT_VIMODES>
			    (sign_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 1 "register_operand"))
			    (zero_extend:<EXT_VIMODES>
			      (vec_duplicate:VIMODES
				(match_operand:<VSUBMODE> 2 "register_operand"))))
			(match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumul<mode>3_highpart_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(truncate:VIMODES
	     (ashiftrt:<EXT_VIMODES>
	       (mult:<EXT_VIMODES>
		 (sign_extend:<EXT_VIMODES>
		   (match_operand:VIMODES 1 "register_operand" "vr"))
		 (zero_extend:<EXT_VIMODES>
		   (vec_duplicate:VIMODES
		     (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	       (match_operand:<EXT_VIMODES> 3 "shift_<vmsize>_operand" "Ws<vmsize>")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulhsu.vx\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mulh<v_su><mode>_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (match_operand:<VSUBMODE> 2 "register_operand")]
		       UNSPEC_VMULH)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
  rtx shift = GEN_INT (GET_MODE_UNIT_BITSIZE (<VIMODES:MODE>mode));
  emit_insn (gen_<v_su>mul<mode>3_highpart_scalar (operands[0], operands[1],
						   operands[2], shift));
  DONE;
})

;;Multiply with vector-vector for vector masking type
;;and returning high bits of product

(define_expand "<su>mul<mode>3_highpart_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VIMODES
			  (ashiftrt:<EXT_VIMODES>
			    (mult:<EXT_VIMODES>
			      (any_extend:<EXT_VIMODES>
				(match_operand:VIMODES 3 "register_operand"))
			      (any_extend:<EXT_VIMODES>
				(match_operand:VIMODES 4 "register_operand")))
			    (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<su>mul<mode>3_highpart_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (truncate:VIMODES
	       (ashiftrt:<EXT_VIMODES>
		 (mult:<EXT_VIMODES>
		   (any_extend:<EXT_VIMODES>
		     (match_operand:VIMODES 3 "register_operand" "vr"))
		   (any_extend:<EXT_VIMODES>
		     (match_operand:VIMODES 4 "register_operand" "vr")))
		 (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand" "Ws<vmsize>")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulh<u>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumul<mode>3_highpart_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VIMODES
			(ashiftrt:<EXT_VIMODES>
			  (mult:<EXT_VIMODES>
			    (sign_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 3 "register_operand"))
			    (zero_extend:<EXT_VIMODES>
			      (match_operand:VIMODES 4 "register_operand")))
			  (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumul<mode>3_highpart_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (truncate:VIMODES
	       (ashiftrt:<EXT_VIMODES>
		 (mult:<EXT_VIMODES>
		   (sign_extend:<EXT_VIMODES>
		     (match_operand:VIMODES 3 "register_operand" "vr"))
		   (zero_extend:<EXT_VIMODES>
		     (match_operand:VIMODES 4 "register_operand" "vr")))
		 (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand" "Ws<vmsize>")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulhsu.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mulh<v_su><mode>_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (match_operand:VIMODES 4 "register_operand")]
			 UNSPEC_VMULH)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
  rtx shift = GEN_INT (GET_MODE_UNIT_BITSIZE (<VIMODES:MODE>mode));
  emit_insn (gen_<v_su>mul<mode>3_highpart_mask (operands[0], operands[1],
						 operands[2], operands[3],
						 operands[4], shift));
  DONE;
})

;;Multiply with vector-scalar for vector masking type
;;and returning high bits of product

(define_expand "<su>mul<mode>3_highpart_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VIMODES
			  (ashiftrt:<EXT_VIMODES>
			    (mult:<EXT_VIMODES>
			      (any_extend:<EXT_VIMODES>
				(match_operand:VIMODES 3 "register_operand"))
			      (any_extend:<EXT_VIMODES>
				(vec_duplicate:VIMODES
				  (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<su>mul<mode>3_highpart_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (truncate:VIMODES
	       (ashiftrt:<EXT_VIMODES>
		 (mult:<EXT_VIMODES>
		   (any_extend:<EXT_VIMODES>
		     (match_operand:VIMODES 3 "register_operand" "vr"))
		   (any_extend:<EXT_VIMODES>
		     (vec_duplicate:VIMODES
		       (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
		 (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand" "Ws<vmsize>")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulh<u>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumul<mode>3_highpart_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VIMODES
			  (ashiftrt:<EXT_VIMODES>
			    (mult:<EXT_VIMODES>
			      (sign_extend:<EXT_VIMODES>
				(match_operand:VIMODES 3 "register_operand"))
			      (zero_extend:<EXT_VIMODES>
				(vec_duplicate:VIMODES
				  (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
			  (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumul<mode>3_highpart_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (truncate:VIMODES
	       (ashiftrt:<EXT_VIMODES>
		 (mult:<EXT_VIMODES>
		   (sign_extend:<EXT_VIMODES>
		     (match_operand:VIMODES 3 "register_operand" "vr"))
		   (zero_extend:<EXT_VIMODES>
		     (vec_duplicate:VIMODES
		       (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
	       (match_operand:<EXT_VIMODES> 5 "shift_<vmsize>_operand" "Ws<vmsize>")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vmulhsu.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mulh<v_su><mode>_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (match_operand:<VSUBMODE> 4 "register_operand")]
			 UNSPEC_VMULH)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
  rtx shift = GEN_INT (GET_MODE_UNIT_BITSIZE (<VIMODES:MODE>mode));
  emit_insn (gen_<v_su>mul<mode>3_highpart_scalar_mask (operands[0],
							operands[1],
							operands[2],
							operands[3],
							operands[4],
							shift));
  DONE;
})

;; FP widen multiply instructions.

(define_expand "fwmul<mode>_vv"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(mult:<VWMODE>
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 1 "register_operand"))
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fwmul<mode>_vv_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(mult:<VWMODE>
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 1 "register_operand" "vr"))
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfwmul.vv\t%0,%1,%2"
  [(set_attr "type" "vfwmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fwmul<mode>_vv_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(mult:<VWMODE>
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 1 "register_operand"))
			(float_extend:<VWMODE>
			  (vec_duplicate:VWFMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fwmul<mode>_vv_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(mult:<VWMODE>
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 1 "register_operand" "vr"))
	     (float_extend:<VWMODE>
	       (vec_duplicate:VWFMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "f"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfwmul.vf\t%0,%1,%2"
  [(set_attr "type" "vfwmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fwmul<mode>_vv_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:<VWMODE>
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 3 "register_operand"))
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fwmul<mode>_vv_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:<VWMODE>
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 3 "register_operand" "vr"))
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfwmul.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfwmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "fwmul<mode>_vv_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:<VWMODE>
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 3 "register_operand"))
			  (float_extend:<VWMODE>
			    (vec_duplicate:VWFMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*fwmul<mode>_vv_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:<VWMODE>
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 3 "register_operand" "vr"))
	       (float_extend:<VWMODE>
		 (vec_duplicate:VWFMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "f"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfwmul.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfwmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP multiply accumulate instructions.

(define_expand "vf<vfmadd_sub><mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(mult:VFMODES
			  (match_operand:VFMODES 2 "register_operand")
			  (match_operand:VFMODES 1 "register_operand"))
			(match_operand:VFMODES 3 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_expand "vf<vfmac><mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(mult:VFMODES
			  (match_operand:VFMODES 2 "register_operand")
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_VMACS))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

;; ??? Should use fma as this is a merged mult/add.  And fms below.
(define_expand "fm<as><mode>4"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(mult:VFMODES
			  (match_operand:VFMODES 1 "register_operand")
			  (match_operand:VFMODES 2 "register_operand"))
			(match_operand:VFMODES 3 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*fm<as><mode>4_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(add_sub:VFMODES
	     (mult:VFMODES
	       (match_operand:VFMODES 1 "register_operand" "vr")
	       (match_operand:VFMODES 2 "register_operand" "vr"))
	     (match_operand:VFMODES 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_VMACS))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfmac>.vv\t%0,%1,%2"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; ??? The constant is pulled out of the loop before it can be used here.
(define_insn "*fm<as><mode>4_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr,vr")
	(unspec:VFMODES
	  [(add_sub:VFMODES
	     (mult:VFMODES
	       (match_operand:VFMODES 1 "register_operand" "vr,vr")
	       (match_operand:VFMODES 2 "register_operand" "0,vr"))
	     (match_operand:VFMODES 3 "register_operand" "vr,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "@
   vf<vfmadd_sub>.vv\t%0,%1,%3
   vf<vfmac>.vv\t%0,%1,%2"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfmadd_sub><mode>_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(mult:VFMODES
			  (vec_duplicate:VFMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))
			  (match_operand:VFMODES 1 "register_operand"))
			(match_operand:VFMODES 3 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_expand "vf<vfmac><mode>_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(mult:VFMODES
			  (vec_duplicate:VFMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_VMACS))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*fm<as><mode>4_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(add_sub:VFMODES
	     (mult:VFMODES
	       (vec_duplicate:VFMODES
		 (match_operand:<VSUBMODE> 1 "register_operand" "f"))
	       (match_operand:VFMODES 2 "register_operand" "vr"))
	     (match_operand:VFMODES 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_VMACS))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfmac>.vf\t%0,%1,%2"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "*fm<as><mode>4_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr,vr")
	(unspec:VFMODES
	  [(add_sub:VFMODES
	     (mult:VFMODES
	       (vec_duplicate:VFMODES
		 (match_operand:<VSUBMODE> 1 "register_operand" "f,f"))
	       (match_operand:VFMODES 2 "register_operand" "0,vr"))
	     (match_operand:VFMODES 3 "register_operand" "vr,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "@
   vf<vfmadd_sub>.vf\t%0,%1,%3
   vf<vfmac>.vf\t%0,%1,%2"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfmadd_sub><mode>_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (mult:VFMODES
			     (match_operand:VFMODES 3 "register_operand")
			     (match_operand:VFMODES 2 "register_operand"))
			   (match_operand:VFMODES 4 "register_operand"))]
		       UNSPEC_MASK_VFMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfmadd_sub><mode>_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(mult:VFMODES
		  (match_operand:VFMODES 3 "register_operand" "vr")
		  (match_operand:VFMODES 2 "register_operand" "0"))
		(match_operand:VFMODES 4 "register_operand" "vr"))]
	   UNSPEC_MASK_VFMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfmadd_sub>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfmac><mode>_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (mult:VFMODES
			     (match_operand:VFMODES 3 "register_operand")
			     (match_operand:VFMODES 4 "register_operand"))
			   (match_operand:VFMODES 2 "register_operand"))]
		       UNSPEC_MASK_VFMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfmac><mode>_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(mult:VFMODES
		  (match_operand:VFMODES 3 "register_operand" "vr")
		  (match_operand:VFMODES 4 "register_operand" "vr"))
		(match_operand:VFMODES 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfmac>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfmadd_sub><mode>_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (mult:VFMODES
			     (vec_duplicate:VFMODES
			       (match_operand:<VSUBMODE> 3 "register_operand"))
			     (match_operand:VFMODES 2 "register_operand"))
			   (match_operand:VFMODES 4 "register_operand"))]
		       UNSPEC_MASK_VFMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfmadd_sub><mode>_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(mult:VFMODES
		  (vec_duplicate:VFMODES
		    (match_operand:<VSUBMODE> 3 "register_operand" "f"))
		  (match_operand:VFMODES 2 "register_operand" "0"))
		(match_operand:VFMODES 4 "register_operand" "vr"))]
	    UNSPEC_MASK_VFMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfmadd_sub>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfmac><mode>_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (mult:VFMODES
			     (vec_duplicate:VFMODES
			       (match_operand:<VSUBMODE> 3 "register_operand"))
			     (match_operand:VFMODES 4 "register_operand"))
			   (match_operand:VFMODES 2 "register_operand"))]
		       UNSPEC_MASK_VFMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfmac><mode>_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(mult:VFMODES
		  (vec_duplicate:VFMODES
		    (match_operand:<VSUBMODE> 3 "register_operand" "f"))
		  (match_operand:VFMODES 4 "register_operand" "vr"))
		(match_operand:VFMODES 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfmac>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP multiply negate accumulate instructions.

(define_expand "vf<vfnmadd_sub><mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(neg:VFMODES
			  (mult:VFMODES
			    (match_operand:VFMODES 2 "register_operand")
			    (match_operand:VFMODES 1 "register_operand")))
			(match_operand:VFMODES 3 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_expand "vf<vfnmac><mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(neg:VFMODES
			  (mult:VFMODES
			    (match_operand:VFMODES 2 "register_operand")
			    (match_operand:VFMODES 3 "register_operand")))
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_expand "fnm<sa><mode>4"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(neg:VFMODES
			  (mult:VFMODES
			    (match_operand:VFMODES 1 "register_operand")
			    (match_operand:VFMODES 2 "register_operand")))
			(match_operand:VFMODES 3 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*fnm<sa><mode>4_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr,vr")
	(unspec:VFMODES
	  [(add_sub:VFMODES
	     (neg:VFMODES
	       (mult:VFMODES
		 (match_operand:VFMODES 1 "register_operand" "vr,vr")
		 (match_operand:VFMODES 2 "register_operand" "0,vr")))
	     (match_operand:VFMODES 3 "register_operand" "vr,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "@
   vf<vfnmadd_sub>.vv\t%0,%1,%3
   vf<vfnmac>.vv\t%0,%1,%2"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfnmadd_sub><mode>_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(neg:VFMODES
			  (mult:VFMODES
			    (vec_duplicate:VFMODES
			      (match_operand:<VSUBMODE> 2 "register_operand"))
			    (match_operand:VFMODES 1 "register_operand")))
			(match_operand:VFMODES 3 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_expand "vf<vfnmac><mode>_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(add_sub:VFMODES
			(neg:VFMODES
			  (mult:VFMODES
			    (vec_duplicate:VFMODES
			      (match_operand:<VSUBMODE> 2 "register_operand"))
			    (match_operand:VFMODES 3 "register_operand")))
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*fnm<sa><mode>4_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr,vr")
	(unspec:VFMODES
	  [(add_sub:VFMODES
	     (neg:VFMODES
	       (mult:VFMODES
		 (vec_duplicate:VFMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "f,f"))
		 (match_operand:VFMODES 2 "register_operand" "0,vr")))
	       (match_operand:VFMODES 3 "register_operand" "vr,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "@
   vf<vfnmadd_sub>.vf\t%0,%1,%3
   vf<vfnmac>.vf\t%0,%1,%2"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfnmadd_sub><mode>_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (neg:VFMODES
			     (mult:VFMODES
			       (match_operand:VFMODES 3 "register_operand")
			       (match_operand:VFMODES 2 "register_operand")))
			   (match_operand:VFMODES 4 "register_operand"))]
		       UNSPEC_MASK_VFNMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfnmadd_sub><mode>_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(neg:VFMODES
		  (mult:VFMODES
		    (match_operand:VFMODES 3 "register_operand" "vr")
		    (match_operand:VFMODES 2 "register_operand" "0")))
		(match_operand:VFMODES 4 "register_operand" "vr"))]
	    UNSPEC_MASK_VFNMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfnmadd_sub>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfnmac><mode>_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (neg:VFMODES
			     (mult:VFMODES
			       (match_operand:VFMODES 3 "register_operand")
			       (match_operand:VFMODES 4 "register_operand")))
			   (match_operand:VFMODES 2 "register_operand"))]
		       UNSPEC_MASK_VFNMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfnmac><mode>_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(neg:VFMODES
		  (mult:VFMODES
		    (match_operand:VFMODES 3 "register_operand" "vr")
		    (match_operand:VFMODES 4 "register_operand" "vr")))
		(match_operand:VFMODES 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFNMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfnmac>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfnmadd_sub><mode>_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (neg:VFMODES
			     (mult:VFMODES
			       (vec_duplicate:VFMODES
				 (match_operand:<VSUBMODE> 3 "register_operand"))
			       (match_operand:VFMODES 2 "register_operand")))
			     (match_operand:VFMODES 4 "register_operand"))]
		       UNSPEC_MASK_VFNMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfnmadd_sub><mode>_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(neg:VFMODES
		  (mult:VFMODES
		    (vec_duplicate:VFMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "f"))
		    (match_operand:VFMODES 2 "register_operand" "0")))
		(match_operand:VFMODES 4 "register_operand" "vr"))]
	    UNSPEC_MASK_VFNMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfnmadd_sub>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vf<vfnmac><mode>_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:VFMODES
			   (neg:VFMODES
			     (mult:VFMODES
			       (vec_duplicate:VFMODES
				 (match_operand:<VSUBMODE> 3 "register_operand"))
			       (match_operand:VFMODES 4 "register_operand")))
			   (match_operand:VFMODES 2 "register_operand"))]
		       UNSPEC_MASK_VFNMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vf<vfnmac><mode>_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:VFMODES
		(neg:VFMODES
		  (mult:VFMODES
		    (vec_duplicate:VFMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "f"))
		    (match_operand:VFMODES 4 "register_operand" "vr")))
		(match_operand:VFMODES 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFNMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vf<vfnmac>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP widen multiply accumulate instructions.

(define_expand "vfw<vfmac><mode>"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(mult:<VWMODE>
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 2 "register_operand"))
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 3 "register_operand")))
			 (match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfmac><mode>_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (mult:<VWMODE>
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 1 "register_operand" "vr"))
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 2 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfmac>.vv\t%0,%1,%2"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfw<vfmac><mode>_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(mult:<VWMODE>
			  (vec_duplicate:<VWMODE>
			    (float_extend:<VWMODE>
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (float_extend:<VWMODE>
			    (match_operand:VWFMODES 3 "register_operand")))
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfmac><mode>_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (mult:<VWMODE>
	       (vec_duplicate:<VWMODE>
		 (float_extend:<VWMODE>
		   (match_operand:<VSUBMODE> 2 "register_operand" "f")))
	       (float_extend:<VWMODE>
		 (match_operand:VWFMODES 1 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfmac>.vf\t%0,%2,%1"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfw<vfmac><mode>_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:<VWMODE>
			   (mult:<VWMODE>
			     (float_extend:<VWMODE>
			       (match_operand:VWFMODES 3 "register_operand"))
			     (float_extend:<VWMODE>
			       (match_operand:VWFMODES 4 "register_operand")))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_MASK_VFWMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfmac><mode>_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:<VWMODE>
		(mult:<VWMODE>
		  (float_extend:<VWMODE>
		    (match_operand:VWFMODES 3 "register_operand" "vr"))
		  (float_extend:<VWMODE>
		    (match_operand:VWFMODES 4 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFWMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfmac>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfw<vfmac><mode>_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:<VWMODE>
			   (mult:<VWMODE>
			     (vec_duplicate:<VWMODE>
			       (float_extend:<VWMODE>
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			       (float_extend:<VWMODE>
			    (match_operand:VWFMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_MASK_VFWMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfmac><mode>_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:<VWMODE>
		(mult:<VWMODE>
		  (vec_duplicate:<VWMODE>
		    (float_extend:<VWMODE>
		      (match_operand:<VSUBMODE> 4 "register_operand" "f")))
		    (float_extend:<VWMODE>
		      (match_operand:VWFMODES 3 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFWMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfmac>.vf\t%0,%4,%3,%1.t"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP widen negate multiply accumulate instructions.

(define_expand "vfw<vfnmac><mode>"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(neg:<VWMODE>
			  (mult:<VWMODE>
			    (float_extend:<VWMODE>
			      (match_operand:VWFMODES 2 "register_operand"))
			    (float_extend:<VWMODE>
			      (match_operand:VWFMODES 3 "register_operand"))))
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfnmac><mode>_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (neg:<VWMODE>
	       (mult:<VWMODE>
		 (float_extend:<VWMODE>
		   (match_operand:VWFMODES 1 "register_operand" "vr"))
		 (float_extend:<VWMODE>
		   (match_operand:VWFMODES 2 "register_operand" "vr"))))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfnmac>.vv\t%0,%1,%2"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfw<vfnmac><mode>_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(add_sub:<VWMODE>
			(neg:<VWMODE>
			  (mult:<VWMODE>
			    (vec_duplicate:<VWMODE>
			      (float_extend:<VWMODE>
				(match_operand:<VSUBMODE> 2 "register_operand")))
			      (float_extend:<VWMODE>
				(match_operand:VWFMODES 3 "register_operand"))))
		        (match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfnmac><mode>_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(add_sub:<VWMODE>
	     (neg:<VWMODE>
	       (mult:<VWMODE>
		 (vec_duplicate:<VWMODE>
		   (float_extend:<VWMODE>
		     (match_operand:<VSUBMODE> 2 "register_operand" "f")))
		   (float_extend:<VWMODE>
		     (match_operand:VWFMODES 1 "register_operand" "vr"))))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfnmac>.vf\t%0,%2,%1"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfw<vfnmac><mode>_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:<VWMODE>
			   (neg:<VWMODE>
			     (mult:<VWMODE>
			       (float_extend:<VWMODE>
				 (match_operand:VWFMODES 3 "register_operand"))
			       (float_extend:<VWMODE>
				 (match_operand:VWFMODES 4 "register_operand"))))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_MASK_VFWNMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfnmac><mode>_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:<VWMODE>
		(neg:<VWMODE>
		  (mult:<VWMODE>
		    (float_extend:<VWMODE>
		      (match_operand:VWFMODES 3 "register_operand" "vr"))
		    (float_extend:<VWMODE>
		      (match_operand:VWFMODES 4 "register_operand" "vr"))))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFWNMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfnmac>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfw<vfnmac><mode>_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (add_sub:<VWMODE>
			   (neg:<VWMODE>
			     (mult:<VWMODE>
			       (vec_duplicate:<VWMODE>
				 (float_extend:<VWMODE>
				   (match_operand:<VSUBMODE> 3 "register_operand")))
				 (float_extend:<VWMODE>
				   (match_operand:VWFMODES 4 "register_operand"))))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_MASK_VFWNMACC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfw<vfnmac><mode>_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (add_sub:<VWMODE>
		(neg:<VWMODE>
		  (mult:<VWMODE>
		    (vec_duplicate:<VWMODE>
		      (float_extend:<VWMODE>
			(match_operand:<VSUBMODE> 4 "register_operand" "f")))
		      (float_extend:<VWMODE>
			(match_operand:VWFMODES 3 "register_operand" "vr"))))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_MASK_VFWNMACC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfw<vfnmac>.vf\t%0,%4,%3,%1.t"
  [(set_attr "type" "vfwmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Integer merge instructions.

(define_expand "vec_duplicate<mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(vec_duplicate:VIMODES
			(match_operand:<VSUBMODE> 1 "reg_or_simm5_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "vec_duplicate<mode>_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(vec_duplicate:VIMODES
	     (match_operand:<VSUBMODE> 1 "reg_or_simm5_operand" "r,Ws5"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vmv.v.x\t%0,%1
   vmv.v.i\t%0,%1"
  [(set_attr "type" "vmove,vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP merge instructions.

(define_expand "vec_duplicate<mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(vec_duplicate:VFMODES
			(match_operand:<VSUBMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "vec_duplicate<mode>_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr,*vr")
	(unspec:VFMODES
	  [(vec_duplicate:VFMODES
	     (match_operand:<VSUBMODE> 1 "register_operand" "f,r"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "@
   vfmv.v.f\t%0,%1
   vmv.v.x\t%0,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; FP sign-injection instructions.

(define_expand "<copysign><mode>3"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:VFMODES 1 "register_operand")
			 (match_operand:VFMODES 2 "register_operand")]
		       UNSPEC_COPYSIGNS)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<copysign><mode>3_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:VFMODES 1 "register_operand" "vr")
	      (match_operand:VFMODES 2 "register_operand" "vr")]
	    UNSPEC_COPYSIGNS)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfsgnj<nx>.vv\t%0,%1,%2"
  [(set_attr "type" "vfsgnj")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<copysign><mode>3_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:VFMODES 1 "register_operand")
			 (vec_duplicate:VFMODES
			   (match_operand:<VSUBMODE> 2 "register_operand"))]
		       UNSPEC_COPYSIGNS)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<copysign><mode>3_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:VFMODES 1 "register_operand" "vr")
	      (vec_duplicate:VFMODES
		(match_operand:<VSUBMODE> 2 "register_operand" "f"))]
	    UNSPEC_COPYSIGNS)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfsgnj<nx>.vf\t%0,%1,%2"
  [(set_attr "type" "vfsgnj")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<copysign><mode>3_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VFMODES
			  [(match_operand:VFMODES 3 "register_operand")
			   (match_operand:VFMODES 4 "register_operand")]
			 UNSPEC_COPYSIGNS)
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<copysign><mode>3_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VFMODES
	       [(match_operand:VFMODES 3 "register_operand" "vr")
		(match_operand:VFMODES 4 "register_operand" "vr")]
	      UNSPEC_COPYSIGNS)
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfsgnj<nx>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfsgnj")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<copysign><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VFMODES
			  [(match_operand:VFMODES 3 "register_operand")
			   (vec_duplicate:VFMODES
			     (match_operand:<VSUBMODE> 4 "register_operand"))]
			 UNSPEC_COPYSIGNS)
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<copysign><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VFMODES
	       [(match_operand:VFMODES 3 "register_operand" "vr")
		(vec_duplicate:VFMODES
		  (match_operand:<VSUBMODE> 4 "register_operand" "f"))]
	      UNSPEC_COPYSIGNS)
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfsgnj<nx>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfsgnj")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector comparison support.

;; XXX: Must expand unsupported comparison type.
;; Std pattern vec_cmpu and vec_cmpeq might need implement,
;; but we don't implement auto-vec yet, so may not meaningful yet?
(define_expand "vec_cmp<mode><vmaskmode>"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(match_operator:<VCMPEQUIV> 1 "ordered_comparison_operator"
			[(match_operand:VIMODES 2 "register_operand")
			 (match_operand:VIMODES 3 "vector_arith_operand")])
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
  if (ltge_operator (operands[1], <VCMPEQUIV>mode)
      && !ltge_vector_arith_operand(operands[3], <MODE>mode))
    FAIL;
})

(define_insn "*vec_cmp<mode><vmaskmode>_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr, &vr")
	(unspec:<VCMPEQUIV>
	  [(match_operator:<VCMPEQUIV> 1 "vector_comparison_operator"
	     [(match_operand:VIMODES 2 "register_operand" "vr, vr")
	      (match_operand:VIMODES 3 "vector_arith_operand" "vr, vi")])
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "@
  vms%B1.vv\t%0,%2,%3
  vms%B1.vi\t%0,%2,%v3"
 [(set_attr "type" "vcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "*ltge<mode><vmaskmode>_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr, &vr")
	(unspec:<VCMPEQUIV>
	  [(match_operator:<VCMPEQUIV> 1 "ltge_operator"
	     [(match_operand:VIMODES 2 "register_operand" "vr, vr")
	      (match_operand:VIMODES 3 "ltge_vector_arith_operand" "vr, vj")])
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "@
  vms%B1.vv\t%0,%2,%3
  vms%B1.vi\t%0,%2,%v3"
 [(set_attr "type" "vcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_cmp<mode><vmaskmode>_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 5 "register_operand")
			(match_operator:<VCMPEQUIV> 1 "ordered_comparison_operator"
			  [(match_operand:VIMODES 2 "register_operand")
			   (match_operand:VIMODES 3 "vector_arith_operand")])
			(match_operand:<VCMPEQUIV> 4 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
  if (ltge_operator (operands[1], <VCMPEQUIV>mode)
      && !ltge_vector_arith_operand(operands[3], <MODE>mode))
    FAIL;
})

(define_insn "*vec_cmp<mode><vmaskmode>_mask_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr, &vr")
	(unspec:<VCMPEQUIV>
	  [(if_then_else:<VCMPEQUIV>
	     (match_operand:<VCMPEQUIV> 5 "register_operand" "vm, vm")
	     (match_operator:<VCMPEQUIV> 1 "vector_comparison_operator"
	       [(match_operand:VIMODES 2 "register_operand" "vr, vr")
		(match_operand:VIMODES 3 "vector_arith_operand" "vr, vi")])
	     (match_operand:<VCMPEQUIV> 4 "register_operand" "0, 0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "@
  vms%B1.vv\t%0,%2,%3,%5.t
  vms%B1.vi\t%0,%2,%v3,%5.t"
 [(set_attr "type" "vcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "*ltge<mode><vmaskmode>_mask_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr, &vr")
	(unspec:<VCMPEQUIV>
	  [(if_then_else:<VCMPEQUIV>
	     (match_operand:<VCMPEQUIV> 5 "register_operand" "vm, vm")
	     (match_operator:<VCMPEQUIV> 1 "ltge_operator"
	       [(match_operand:VIMODES 2 "register_operand" "vr, vr")
		(match_operand:VIMODES 3 "ltge_vector_arith_operand" "vr, vj")])
	     (match_operand:<VCMPEQUIV> 4 "register_operand" "0, 0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "@
  vms%B1.vv\t%0,%2,%3,%5.t
  vms%B1.vi\t%0,%2,%v3,%5.t"
 [(set_attr "type" "vcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "*vec_cmp<mode><vmaskmode>_scalar_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(match_operator:<VCMPEQUIV> 1 "ordered_comparison_operator"
	     [(match_operand:VIMODES 2 "register_operand" "vr")
	      (vec_duplicate:VIMODES
		(match_operand:<VSUBMODE> 3 "register_operand" "r"))])
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "vms%B1.vx\t%0,%2,%3"
 [(set_attr "type" "vcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "*vec_cmp<mode><vmaskmode>_scalar_mask_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(if_then_else:<VCMPEQUIV>
	     (match_operand:<VCMPEQUIV> 5 "register_operand" "vm")
	     (match_operator:<VCMPEQUIV> 1 "ordered_comparison_operator"
	       [(match_operand:VIMODES 2 "register_operand" "vr")
		(vec_duplicate:VIMODES
		  (match_operand:<VSUBMODE> 3 "register_operand" "r"))])
	     (match_operand:<VCMPEQUIV> 4 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "vms%B1.vx\t%0,%2,%3,%5.t"
 [(set_attr "type" "vcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_cmp<mode><vmaskmode>"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(match_operator:<VCMPEQUIV> 1 "comparison_operator"
			[(match_operand:VFMODES 2 "register_operand")
			 (match_operand:VFMODES 3 "register_operand")])
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vec_cmp<mode><vmaskmode>_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(match_operator:<VCMPEQUIV> 1 "comparison_operator"
	     [(match_operand:VFMODES 2 "register_operand" "vr")
	      (match_operand:VFMODES 3 "register_operand" "vr")])
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
 "vmf%B1.vv\t%0,%2,%3"
 [(set_attr "type" "vfcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_cmp<mode><vmaskmode>_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(match_operator:<VCMPEQUIV> 1 "comparison_operator"
			[(match_operand:VFMODES 2 "register_operand")
			 (vec_duplicate:VFMODES
			   (match_operand:<VSUBMODE> 3 "register_operand"))])
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vec_cmp<mode><vmaskmode>_scalar_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(match_operator:<VCMPEQUIV> 1 "comparison_operator"
	     [(match_operand:VFMODES 2 "register_operand" "vr")
	      (vec_duplicate:VFMODES
		(match_operand:<VSUBMODE> 3 "register_operand" "f"))])
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
 "vmf%B1.vf\t%0,%2,%3"
 [(set_attr "type" "vfcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_cmp<mode><vmaskmode>_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 5 "register_operand")
			(match_operator:<VCMPEQUIV> 1 "comparison_operator"
			  [(match_operand:VFMODES 2 "register_operand")
			   (match_operand:VFMODES 3 "register_operand")])
			(match_operand:<VCMPEQUIV> 4 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vec_cmp<mode><vmaskmode>_mask_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(if_then_else:<VCMPEQUIV>
	     (match_operand:<VCMPEQUIV> 5 "register_operand" "vm")
	     (match_operator:<VCMPEQUIV> 1 "comparison_operator"
	       [(match_operand:VFMODES 2 "register_operand" "vr")
		(match_operand:VFMODES 3 "register_operand" "vr")])
	     (match_operand:<VCMPEQUIV> 4 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
 "vmf%B1.vv\t%0,%2,%3,%5.t"
 [(set_attr "type" "vfcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vec_cmp<mode><vmaskmode>_scalar_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 5 "register_operand")
			(match_operator:<VCMPEQUIV> 1 "comparison_operator"
			  [(match_operand:VFMODES 2 "register_operand")
			   (vec_duplicate:VFMODES
			     (match_operand:<VSUBMODE> 3 "register_operand"))])
			(match_operand:<VCMPEQUIV> 4 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vec_cmp<mode><vmaskmode>_scalar_mask_nosetvl"
  [(set (match_operand:<VCMPEQUIV> 0 "register_operand" "=&vr")
	(unspec:<VCMPEQUIV>
	  [(if_then_else:<VCMPEQUIV>
	     (match_operand:<VCMPEQUIV> 5 "register_operand" "vm")
	     (match_operator:<VCMPEQUIV> 1 "comparison_operator"
	       [(match_operand:VFMODES 2 "register_operand" "vr")
		(vec_duplicate:VFMODES
		  (match_operand:<VSUBMODE> 3 "register_operand" "f"))])
	     (match_operand:<VCMPEQUIV> 4 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
 "vmf%B1.vf\t%0,%2,%3,%5.t"
 [(set_attr "type" "vfcmp")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mov<mode>cc"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(match_operand:VIMODES 3 "vector_arith_operand")
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
})

(define_insn "mov<mode>cc_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vd,vd")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 3 "register_operand" "vm,vm")
	     (match_operand:VIMODES 2 "vector_arith_operand" "vr, vi")
	     (match_operand:VIMODES 1 "register_operand" "vr,vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "@
  vmerge.vvm\t%0,%1,%2,%3
  vmerge.vim\t%0,%1,%2,%3"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mov<mode>cc_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
})

(define_insn "mov<mode>cc_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vd")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "vmerge.vxm\t%0,%1,%2,%3"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector FP merge.

(define_expand "mov<mode>cc"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(match_operand:VFMODES 3 "register_operand")
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "mov<mode>cc_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vd")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	     (match_operand:VFMODES 2 "register_operand" "vr")
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
 "vmerge.vvm\t%0,%1,%2,%3"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "mov<mode>cc_scalar"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(vec_duplicate:VFMODES
			  (match_operand:<VSUBMODE> 3 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "mov<mode>cc_scalar_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vd")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 3 "register_operand" "vm")
	     (vec_duplicate:VFMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "f"))
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
 "vfmerge.vfm\t%0,%1,%2,%3"
 [(set_attr "type" "vfmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; ??? Missing splitters.

(define_expand "vcond<mode><mode>"
  [(set (match_operand:VIMODES 0 "register_operand")
	(if_then_else:VIMODES
	  (match_operator 3 "comparison_operator"
	    [(match_operand:VIMODES 4 "register_operand")
	     (match_operand:VIMODES 5 "register_operand")])
	  (match_operand:VIMODES 1 "register_operand")
	  (match_operand:VIMODES 2 "register_operand")))]
 "TARGET_VECTOR"
{
  rtx tmp = gen_reg_rtx (<VCMPEQUIV>mode);
  emit_insn (gen_vec_cmp<mode><vmaskmode> (tmp, operands[3],
					   operands[4], operands[5]));
  emit_insn (gen_mov<mode>cc_nosetvl (operands[0], operands[1],
				      operands[2], tmp));
  DONE;
})

(define_expand "vcond<mode><mode>"
  [(set (match_operand:VFMODES 0 "register_operand")
	(if_then_else:VFMODES
	  (match_operator 3 "comparison_operator"
	    [(match_operand:VFMODES 4 "register_operand")
	     (match_operand:VFMODES 5 "register_operand")])
	  (match_operand:VFMODES 1 "register_operand")
	  (match_operand:VFMODES 2 "register_operand")))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
  rtx tmp = gen_reg_rtx (<VCMPEQUIV>mode);
  emit_insn (gen_vec_cmp<mode><vmaskmode> (tmp, operands[3],
					   operands[4], operands[5]));
  emit_insn (gen_mov<mode>cc_nosetvl (operands[0], operands[1],
				      operands[2], tmp));
  DONE;
})

(define_expand "vcond<mode><vintequiv>"
  [(set (match_operand:VFMODES 0 "register_operand")
	(if_then_else:VFMODES
	  (match_operator 3 "comparison_operator"
	    [(match_operand:<VINTEQUIV> 4 "register_operand")
	     (match_operand:<VINTEQUIV> 5 "register_operand")])
	  (match_operand:VFMODES 1 "register_operand")
	  (match_operand:VFMODES 2 "register_operand")))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
  rtx tmp = gen_reg_rtx (<VCMPEQUIV>mode);
  emit_insn (gen_vec_cmp<vintequiv><vmaskmode> (tmp, operands[3],
					   operands[4], operands[5]));
  emit_insn (gen_mov<mode>cc_nosetvl (operands[0], operands[1],
				      operands[2], tmp));
  DONE;
})

(define_expand "vcond<vintequiv><mode>"
  [(set (match_operand:<VINTEQUIV> 0 "register_operand")
	(if_then_else:<VINTEQUIV>
	  (match_operator 3 "comparison_operator"
	    [(match_operand:VFMODES 4 "register_operand")
	     (match_operand:VFMODES 5 "register_operand")])
	  (match_operand:<VINTEQUIV> 1 "register_operand")
	  (match_operand:<VINTEQUIV> 2 "register_operand")))]
 "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
  rtx tmp = gen_reg_rtx (<VCMPEQUIV>mode);
  emit_insn (gen_vec_cmp<mode><vmaskmode> (tmp, operands[3],
					   operands[4], operands[5]));
  emit_insn (gen_mov<vintequiv>cc_nosetvl (operands[0], operands[1],
					   operands[2], tmp));
  DONE;
})

;; Vector mask operations

(define_expand "clr<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES [(match_dup 1)] UNSPEC_VCLR)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
{
  /* Using gen function instead of write (const_vector [(const_int 0)]) because,
     emit-rtl.c:gen_rtx_CONST_VECTOR will check the number of elements is same
     as NUNIT of mode.  */
  operands[1] = gen_const_vec_duplicate (<MODE>mode, const0_rtx);
})

(define_insn "*clr<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	     [(match_operand:VMASKMODES 1 "const_vector_int_0_operand" "v0")]
	    UNSPEC_VCLR)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
  "vmclr.m\t%0"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_expand "set<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES [(match_dup 1)] UNSPEC_VSET)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
{
  /* Using gen function instead of write (const_vector [(const_int 0)]) because,
     emit-rtl.c:gen_rtx_CONST_VECTOR will check the number of elements is same
     as NUNIT of mode.  */
  operands[1] = gen_const_vec_duplicate (<MODE>mode, const1_rtx);
})

(define_insn "*set<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	     [(match_operand:VMASKMODES 1 "const_vector_int_1_operand" "v1")]
	    UNSPEC_VSET)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
  "vmset.m\t%0"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "not<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(not:VMASKMODES
	     (match_operand:VMASKMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
  "vmnot.m\t%0,%1"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

;; Vector Mask Load/Store

(define_expand "lm<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	    [(mem:VMASKMODES (match_operand 1 "register_operand"))]
	    UNSPEC_VLM)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
{
})

(define_insn "*lm<mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	    [(match_operand:VMASKMODES 1 "memory_operand" "m")]
	    UNSPEC_VLM)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
  "vlm.v\t%0,%1"
  [(set_attr "type" "vload")
   (set_attr "mode" "none")])

(define_expand "sm<mode>"
  [(set (mem:VMASKMODES (match_operand 0 "register_operand"))
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	    [(match_operand:VMASKMODES 1 "register_operand")]
	    UNSPEC_VSM)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
{
})

(define_insn "*sm<mode>"
  [(set (match_operand:VMASKMODES 0 "memory_operand" "=m")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	    [(match_operand:VMASKMODES 1 "register_operand" "vr")]
	    UNSPEC_VSM)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
  "vsm.v\t%1,%0"
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")])

;; Vector Mask-Register Logical Instructions

(define_insn "<optab><mode>3"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(any_bitwise:VMASKMODES
	     (match_operand:VMASKMODES 1 "register_operand" "vr")
	     (match_operand:VMASKMODES 2 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 "vm<insn>.mm\t%0,%1,%2"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "<invmaskop><mode>3"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(not:VMASKMODES
	     (any_bitwise:VMASKMODES
	       (match_operand:VMASKMODES 1 "register_operand" "vr")
	       (match_operand:VMASKMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 "vm<invmaskop>.mm\t%0,%1,%2"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "<insn>n<mode>3"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=vr")
	(unspec:VMASKMODES
	  [(any_opnot:VMASKMODES
	     (match_operand:VMASKMODES 1 "register_operand" "vr")
	     (not:VMASKMODES
	       (match_operand:VMASKMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 "vm<insn>not.mm\t%0,%1,%2"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

;; Adapter for built-in function.
(define_expand "or<mode>3"
  [(set (match_operand:VMASKMODES 0 "register_operand")
	(unspec:VMASKMODES
	  [(ior:VMASKMODES
	     (match_operand:VMASKMODES 1 "register_operand")
	     (match_operand:VMASKMODES 2 "register_operand"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
{
})

;; Misc Vector Mask-Register Operations
;; XXX: The popcount operation will be optimize from its scalar mode,
;; instead operands[1]'s vector mode. There is problem, we get wrong
;; upper bound value from mode of popcount operation.
(define_insn "popc<VMASKMODES:mode>2_<P:mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P
	  [(unspec:P
	     [(match_operand:VMASKMODES 1 "register_operand" "vr")]
	    UNSPEC_VPOPCOUNT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 { return target_subset_version_p ("v", 0, 7) ? "vmpopc.m\t%0,%1" : "vcpop.m\t%0,%1"; }
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "popc<VMASKMODES:mode>2_<P:mode>_mask"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P
	  [(unspec:P
	     [(and:VMASKMODES
		(match_operand:VMASKMODES 1 "register_operand" "vm")
		(match_operand:VMASKMODES 2 "register_operand" "vr"))]
	    UNSPEC_VPOPCOUNT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 { return target_subset_version_p ("v", 0, 7) ? "vmpopc.m\t%0,%2,%1.t" : "vcpop.m\t%0,%2,%1.t"; }
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "first<VMASKMODES:mode>2_<P:mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P
	  [(unspec:P
	     [(match_operand:VMASKMODES 1 "register_operand" "vr")]
	    UNSPEC_FIRST)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 { return target_subset_version_p ("v", 0, 7) ? "vmfirst.m\t%0,%1" : "vfirst.m\t%0,%1"; }
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "first<VMASKMODES:mode>2_<P:mode>_mask"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P
	  [(unspec:P
	     [(and:VMASKMODES
		(match_operand:VMASKMODES 1 "register_operand" "vm")
		(match_operand:VMASKMODES 2 "register_operand" "vr"))]
	    UNSPEC_FIRST)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 { return target_subset_version_p ("v", 0, 7) ? "vmfirst.m\t%0,%2,%1.t" : "vfirst.m\t%0,%2,%1.t"; }
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_expand "iota<mode>2"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")]
		       UNSPEC_IOTA)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
})

(define_insn "*iota<mode>2_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vr")]
	    UNSPEC_IOTA)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "viota.m\t%0,%1"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "iota<mode>2_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (match_operand:<VCMPEQUIV> 3 "register_operand")]
		       UNSPEC_IOTA)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
})

(define_insn "*iota<mode>2_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VIMODES 2 "register_operand" "0")
	      (match_operand:<VCMPEQUIV> 3 "register_operand" "vr")]
	    UNSPEC_IOTA)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "viota.m\t%0,%3,%1.t"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vid<mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES [(const_int 0)] UNSPEC_VID)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
})

(define_insn "*vid<mode>_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES [(const_int 0)] UNSPEC_VID)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "vid.v\t%0"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vid<mode>_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")]
		       UNSPEC_VID)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
 "TARGET_VECTOR"
{
})

(define_insn "*vid<mode>_mask"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VIMODES 2 "register_operand" "0")]
	    UNSPEC_VID)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
 "TARGET_VECTOR"
 "vid.v\t%0,%1.t"
 [(set_attr "type" "vmove")
  (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_insn "<misc_maskop><mode>"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=&vr")
	(unspec:VMASKMODES
	  [(unspec:VMASKMODES
	     [(match_operand:VMASKMODES 1 "register_operand" "vr")]
	    MISC_MASK_OP)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 "vm<misc_maskop>.m\t%0,%1"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

(define_insn "<misc_maskop><mode>_mask"
  [(set (match_operand:VMASKMODES 0 "register_operand" "=&vr")
	(unspec:VMASKMODES
	  [(if_then_else:VMASKMODES
	     (match_operand:VMASKMODES 1 "register_operand" "vm")
	     (unspec:VMASKMODES
	       [(match_operand:VMASKMODES 3 "register_operand" "vr")]
	      MISC_MASK_OP)
	     (match_operand:VMASKMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
 "TARGET_VECTOR"
 "vm<misc_maskop>.m\t%0,%3,%1.t"
 [(set_attr "type" "vmask")
  (set_attr "mode" "none")])

;; Adaptor for built-in functions

;; Integer compare functions

(define_expand "<icmp><mode>"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(any_cmp:<VCMPEQUIV>
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "<ltge>vector_arith_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_expand "<icmp><mode>_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_cmp:<VCMPEQUIV>
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "<ltge>vector_arith_operand"))
			(match_operand:<VCMPEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_expand "<icmp><mode>_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(cmp_except_ge:<VCMPEQUIV>
			(match_operand:VIMODES 1 "register_operand")
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

;; We don't have compare with grater then.
;; Using vmslt and vmnand to replace it.
(define_expand "sge<u><mode>_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(any_lt:<VCMPEQUIV>
			(match_operand:VIMODES 1 "register_operand")
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])
   (set (match_dup 0)
	(unspec:<VCMPEQUIV>
	  [(not:<VCMPEQUIV> (and:<VCMPEQUIV> (match_dup 0) (match_dup 0)))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
{
})

;; We don't have compare with grater then.
;; Using vmslt and vmxor to replace it.
(define_expand "<icmp><mode>_scalar_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(cmp_except_ge:<VCMPEQUIV>
			  (match_operand:VIMODES 3 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			(match_operand:<VCMPEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_expand "sge<u><mode>_scalar_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_lt:<VCMPEQUIV>
			  (match_operand:VIMODES 3 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			(match_operand:<VCMPEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])
   (set (match_dup 0)
	(unspec:<VCMPEQUIV>
	  [(xor:<VCMPEQUIV> (match_dup 0) (match_dup 1))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))]
  "TARGET_VECTOR"
{
})

;; FP compare functions

(define_expand "<fcmp><mode>"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(signed_cmp:<VCMPEQUIV>
			(match_operand:VFMODES 1 "register_operand")
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_expand "<fcmp><mode>_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(signed_cmp:<VCMPEQUIV>
			  (match_operand:VFMODES 3 "register_operand")
			  (match_operand:VFMODES 4 "register_operand"))
			(match_operand:<VCMPEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_expand "<fcmp><mode>_scalar"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(signed_cmp:<VCMPEQUIV>
			(match_operand:VFMODES 1 "register_operand")
			(vec_duplicate:VFMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_expand "<fcmp><mode>_scalar_mask"
  [(parallel [(set (match_operand:<VCMPEQUIV> 0 "register_operand")
		   (unspec:<VCMPEQUIV>
		     [(if_then_else:<VCMPEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(signed_cmp:<VCMPEQUIV>
			  (match_operand:VFMODES 3 "register_operand")
			  (vec_duplicate:VFMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			  (match_operand:<VCMPEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

;; Integer Reduction instructions.

(define_expand "reduc_<reduc><mode>"
  [(parallel [(set (match_operand:<V1MODES> 0 "register_operand")
		   (unspec:<V1MODES>
		     [(unspec:<V1MODES>
			[(match_operand:<V1MODES> 1 "register_operand")
			 (any_reduc:VIMODES
			   (vec_duplicate:VIMODES
			     (match_operand:<V1MODES> 2 "register_operand"))
			   (match_operand:VIMODES 3 "register_operand"))]
		       UNSPEC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*reduc_<reduc><mode>_nosetvl"
  [(set (match_operand:<V1MODES> 0 "register_operand" "=vr")
	(unspec:<V1MODES>
	  [(unspec:<V1MODES>
	     [(match_operand:<V1MODES> 1 "register_operand" "0")
	      (any_reduc:VIMODES
		(vec_duplicate:VIMODES
		  (match_operand:<V1MODES> 2 "register_operand" "vr"))
		(match_operand:VIMODES 3 "register_operand" "vr"))]
	    UNSPEC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vred<reduc>.vs\t%0,%3,%2"
  [(set_attr "type" "<any_reduc_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "reduc_<reduc><mode>_mask"
  [(parallel [(set (match_operand:<V1MODES> 0 "register_operand")
		   (unspec:<V1MODES>
		     [(unspec:<V1MODES>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:<V1MODES> 2 "register_operand")
			 (any_reduc:VIMODES
			   (vec_duplicate:VIMODES
			     (match_operand:<V1MODES> 3 "register_operand"))
			   (match_operand:VIMODES 4 "register_operand"))]
		       UNSPEC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*reduc_<reduc><mode>_mask_nosetvl"
  [(set (match_operand:<V1MODES> 0 "register_operand" "=vr")
	(unspec:<V1MODES>
	  [(unspec:<V1MODES>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:<V1MODES> 2 "register_operand" "0")
	      (any_reduc:VIMODES
		(vec_duplicate:VIMODES
		  (match_operand:<V1MODES> 3 "register_operand" "vr"))
		(match_operand:VIMODES 4 "register_operand" "vr"))]
	    UNSPEC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vred<reduc>.vs\t%0,%4,%3,%1.t"
  [(set_attr "type" "<any_reduc_type>")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Widening Integer Reduction Instructions

(define_expand "wreduc_sum<sumu><mode>"
  [(parallel [(set (match_operand:<VW1MODES> 0 "register_operand")
		   (unspec:<VW1MODES>
		     [(unspec:<VW1MODES>
			[(match_operand:<VW1MODES> 1 "register_operand")
			 (match_operand:<VW1MODES> 2 "register_operand")
			 (match_operand:VWRED_IMODES 3 "register_operand")]
		       WREDUC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wreduc_sum<sumu><mode>_nosetvl"
  [(set (match_operand:<VW1MODES> 0 "register_operand" "=&vr")
	(unspec:<VW1MODES>
	  [(unspec:<VW1MODES>
	     [(match_operand:<VW1MODES> 1 "register_operand" "0")
	      (match_operand:<VW1MODES> 2 "register_operand" "vr")
	      (match_operand:VWRED_IMODES 3 "register_operand" "vr")]
	    WREDUC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwredsum<sumu>.vs\t%0,%3,%2"
  [(set_attr "type" "vred_sum")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wreduc_sum<sumu><mode>_mask"
  [(parallel [(set (match_operand:<VW1MODES> 0 "register_operand")
		   (unspec:<VW1MODES>
		     [(unspec:<VW1MODES>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:<VW1MODES> 2 "register_operand")
			 (match_operand:<VW1MODES> 3 "register_operand")
			 (match_operand:VWRED_IMODES 4 "register_operand")]
		       WREDUC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wreduc_sum<sumu><mode>_mask_nosetvl"
  [(set (match_operand:<VW1MODES> 0 "register_operand" "=&vr")
	(unspec:<VW1MODES>
	  [(unspec:<VW1MODES>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:<VW1MODES> 2 "register_operand" "0")
	      (match_operand:<VW1MODES> 3 "register_operand" "vr")
	      (match_operand:VWRED_IMODES 4 "register_operand" "vr")]
	    WREDUC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwredsum<sumu>.vs\t%0,%4,%3,%1.t"
  [(set_attr "type" "vred_sum")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Floating Point Reduction instructions.

(define_expand "reduc_<reduc><mode>"
  [(parallel [(set (match_operand:<V1MODES> 0 "register_operand")
		   (unspec:<V1MODES>
		     [(unspec:<V1MODES>
			[(match_operand:<V1MODES> 1 "register_operand")
			 (any_freduc:VFMODES
			   (vec_duplicate:VFMODES
			     (match_operand:<V1MODES> 2 "register_operand"))
			   (match_operand:VFMODES 3 "register_operand"))]
		       UNSPEC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*reduc_<reduc><mode>_nosetvl"
  [(set (match_operand:<V1MODES> 0 "register_operand" "=vr")
	(unspec:<V1MODES>
	  [(unspec:<V1MODES>
	     [(match_operand:<V1MODES> 1 "register_operand" "0")
	      (any_freduc:VFMODES
		(vec_duplicate:VFMODES
		  (match_operand:<V1MODES> 2 "register_operand" "vr"))
		(match_operand:VFMODES 3 "register_operand" "vr"))]
	    UNSPEC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfred<reduc>.vs\t%0,%3,%2"
  [(set_attr "type" "vfred")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "reduc_<reduc><mode>_mask"
  [(parallel [(set (match_operand:<V1MODES> 0 "register_operand")
		   (unspec:<V1MODES>
		     [(unspec:<V1MODES>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:<V1MODES> 2 "register_operand")
			 (any_freduc:VFMODES
			   (vec_duplicate:VFMODES
			     (match_operand:<V1MODES> 3 "register_operand"))
			   (match_operand:VFMODES 4 "register_operand"))]
		       UNSPEC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*reduc_<reduc><mode>_mask_nosetvl"
  [(set (match_operand:<V1MODES> 0 "register_operand" "=vr")
	(unspec:<V1MODES>
	  [(unspec:<V1MODES>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:<V1MODES> 2 "register_operand" "0")
	      (any_freduc:VFMODES
		(vec_duplicate:VFMODES
		  (match_operand:<V1MODES> 3 "register_operand" "vr"))
		(match_operand:VFMODES 4 "register_operand" "vr"))]
	    UNSPEC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vfred<reduc>.vs\t%0,%4,%3,%1.t"
  [(set_attr "type" "vfred")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "reduc_<order>sum<mode>"
  [(parallel [(set (match_operand:<V1MODES> 0 "register_operand")
		   (unspec:<V1MODES>
		     [(unspec:<V1MODES>
			[(match_operand:<V1MODES> 1 "register_operand")
			 (plus:VFMODES
			   (vec_duplicate:VFMODES
			     (match_operand:<V1MODES> 2 "register_operand"))
			   (match_operand:VFMODES 3 "register_operand"))]
		       FREDUC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*reduc_<order>sum<mode>_nosetvl"
  [(set (match_operand:<V1MODES> 0 "register_operand" "=vr")
	(unspec:<V1MODES>
	  [(unspec:<V1MODES>
	     [(match_operand:<V1MODES> 1 "register_operand" "0")
	      (plus:VFMODES
		(vec_duplicate:VFMODES
		  (match_operand:<V1MODES> 2 "register_operand" "vr"))
		(match_operand:VFMODES 3 "register_operand" "vr"))]
	    FREDUC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return (target_subset_version_p ("v", 0, 7) && !strcmp ("<order>", "u")) ? "vfredsum.vs\t%0,%3,%2" : "vfred<order>sum.vs\t%0,%3,%2"; }
  [(set_attr "type" "vfred")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "reduc_<order>sum<mode>_mask"
  [(parallel [(set (match_operand:<V1MODES> 0 "register_operand")
		   (unspec:<V1MODES>
		     [(unspec:<V1MODES>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:<V1MODES> 2 "register_operand")
			 (plus:VFMODES
			   (vec_duplicate:VFMODES
			     (match_operand:<V1MODES> 3 "register_operand"))
			   (match_operand:VFMODES 4 "register_operand"))]
		       FREDUC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*reduc_<order>sum<mode>_mask_nosetvl"
  [(set (match_operand:<V1MODES> 0 "register_operand" "=vr")
	(unspec:<V1MODES>
	  [(unspec:<V1MODES>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:<V1MODES> 2 "register_operand" "0")
	      (plus:VFMODES
		(vec_duplicate:VFMODES
		  (match_operand:<V1MODES> 3 "register_operand" "vr"))
		(match_operand:VFMODES 4 "register_operand" "vr"))]
	    FREDUC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return (target_subset_version_p ("v", 0, 7) && !strcmp ("<order>", "u")) ? "vfredsum.vs\t%0,%4,%3,%1.t" : "vfred<order>sum.vs\t%0,%4,%3,%1.t"; }
  [(set_attr "type" "vfred")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Widening Floating-Point Reduction Instructions

(define_expand "wreduc_<order>sum<mode>"
  [(parallel [(set (match_operand:<VW1MODES> 0 "register_operand")
		   (unspec:<VW1MODES>
		     [(unspec:<VW1MODES>
			[(match_operand:<VW1MODES> 1 "register_operand")
			 (match_operand:<VW1MODES> 2 "register_operand")
			 (match_operand:VWRED_FMODES 3 "register_operand")]
		       WFREDUC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*wreduc_<order>sum<mode>_nosetvl"
  [(set (match_operand:<VW1MODES> 0 "register_operand" "=&vr")
	(unspec:<VW1MODES>
	  [(unspec:<VW1MODES>
	     [(match_operand:<VW1MODES> 1 "register_operand" "0")
	      (match_operand:<VW1MODES> 2 "register_operand" "vr")
	      (match_operand:VWRED_FMODES 3 "register_operand" "vr")]
	    WFREDUC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return (target_subset_version_p ("v", 0, 7) && !strcmp ("<order>", "u")) ? "vfwredsum.vs\t%0,%3,%2" : "vfwred<order>sum.vs\t%0,%3,%2"; }
  [(set_attr "type" "vfred")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wreduc_<order>sum<mode>_mask"
  [(parallel [(set (match_operand:<VW1MODES> 0 "register_operand")
		   (unspec:<VW1MODES>
		     [(unspec:<VW1MODES>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:<VW1MODES> 2 "register_operand")
			 (match_operand:<VW1MODES> 3 "register_operand")
			 (match_operand:VWRED_FMODES 4 "register_operand")]
		       WFREDUC_REDUC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wreduc_<order>sum<mode>_mask_nosetvl"
  [(set (match_operand:<VW1MODES> 0 "register_operand" "=&vr")
	(unspec:<VW1MODES>
	  [(unspec:<VW1MODES>
	    [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (match_operand:<VW1MODES> 2 "register_operand" "0")
	     (match_operand:<VW1MODES> 3 "register_operand" "vr")
	     (match_operand:VWRED_FMODES 4 "register_operand" "vr")]
	    WFREDUC_REDUC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return (target_subset_version_p ("v", 0, 7) && !strcmp ("<order>", "u")) ? "vfwredsum.vs\t%0,%4,%3,%1.t" : "vfwred<order>sum.vs\t%0,%4,%3,%1.t"; }
  [(set_attr "type" "vfred")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "reinterpret_<mode><vintequiv>"
  [(set (match_operand:VFMODES 0 "register_operand")
	(subreg:VFMODES (match_operand:<VINTEQUIV> 1 "register_operand") 0))]
  "TARGET_VECTOR"
{
  emit_insn (gen_mov<mode> (operands[0],
	     simplify_gen_subreg (<MODE>mode, operands[1],
				  <VINTEQUIV>mode, 0)));
  DONE;
})

(define_expand "reinterpret_<vintequiv><mode>"
  [(set (match_operand:<VINTEQUIV> 0 "register_operand")
	(subreg:<VINTEQUIV> (match_operand:VFMODES 1 "register_operand") 0))]
  "TARGET_VECTOR"
{
  emit_insn (gen_mov<vintequiv> (operands[0],
	     simplify_gen_subreg (<VINTEQUIV>mode, operands[1],
				  <MODE>mode, 0)));
  DONE;
})

(define_expand "reinterpret_<VIMODES2:mode><VIMODES:mode>"
  [(set (match_operand:VIMODES2 0 "register_operand")
	(subreg:VIMODES2 (match_operand:VIMODES 1 "register_operand") 0))]
  "TARGET_VECTOR"
{
  emit_insn (gen_mov<VIMODES2:mode> (operands[0],
	     simplify_gen_subreg (<VIMODES2:MODE>mode, operands[1],
				  <VIMODES:MODE>mode, 0)));
  DONE;
})

(define_expand "reinterpret_<VFMODES2:mode><VFMODES:mode>"
  [(set (match_operand:VFMODES2 0 "register_operand")
	(subreg:VFMODES2 (match_operand:VFMODES 1 "register_operand") 0))]
  "TARGET_VECTOR"
{
  rtx tmp_src, tmp_dst;
  tmp_src = gen_reg_rtx(<VFMODES:VINTEQUIV>mode);
  tmp_dst = gen_reg_rtx(<VFMODES2:VINTEQUIV>mode);
  emit_insn (gen_mov<VFMODES:vintequiv> (tmp_src,
	     simplify_gen_subreg (<VFMODES:VINTEQUIV>mode, operands[1],
				  <VFMODES:MODE>mode, 0)));

  emit_insn (gen_mov<VFMODES2:vintequiv> (tmp_dst,
	     simplify_gen_subreg (<VFMODES2:VINTEQUIV>mode, tmp_src,
				  <VFMODES:VINTEQUIV>mode, 0)));

  emit_insn (gen_mov<VFMODES2:mode> (operands[0],
	     simplify_gen_subreg (<VFMODES2:MODE>mode, tmp_dst,
				  <VFMODES2:VINTEQUIV>mode, 0)));
  DONE;
})


(define_expand "read_vlenb"
  [(match_operand 0 "register_operand")]
  "TARGET_VECTOR"
{
  rtx imm = gen_int_mode (UNITS_PER_V_REG, Pmode);
  emit_move_insn (operands[0], imm);
  DONE;
})

;; Vector Single-Width Bit Shift Instructions

(define_expand "<vshift><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_shift:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "vector_shift_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vshift><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(any_shift:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr,vr")
	     (match_operand:VIMODES 2 "vector_shift_operand" "vr,vk"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%1,%2
   v<insn>.vi\t%0,%1,%v2"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<vshift><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_shift:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vshift><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(any_shift:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%1,%2"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<vshift><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_shift:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "vector_shift_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vshift><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (any_shift:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr,vr")
	       (match_operand:VIMODES 4 "vector_shift_operand" "vr,vk"))
	     (match_operand:VIMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%3,%4,%1.t
   v<insn>.vi\t%0,%3,%v4,%1.t"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<vshift><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_shift:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			  (match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vshift><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_shift:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Narrowing Integer Right Shift Instructions

(define_expand "<vnshift><mode>3_nv"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(truncate:VWIMODES
			(any_shiftrt:<VWMODE>
			  (match_operand:<VWMODE> 1 "register_operand")
			  (match_operand:VWIMODES 2 "vector_shift_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vnshift><mode>3_nv_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VWIMODES
	  [(truncate:VWIMODES
	     (any_shiftrt:<VWMODE>
	       (match_operand:<VWMODE> 1 "register_operand" "vr,vr")
	       (match_operand:VWIMODES 2 "vector_shift_operand" "vr,vk")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  {
    switch (which_alternative)
      {
      case 0:
	return target_subset_version_p ("v", 0, 7) ? "vn<insn>.vv\t%0,%1,%2" : "vn<insn>.wv\t%0,%1,%2";
      case 1:
	return target_subset_version_p ("v", 0, 7) ? "vn<insn>.vi\t%0,%1,%v2" : "vn<insn>.wi\t%0,%1,%v2";
      default:
	gcc_unreachable ();
      }
  }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<vnshift><mode>3_nv_scalar"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(truncate:VWIMODES
			(any_shiftrt:<VWMODE>
			  (match_operand:<VWMODE> 1 "register_operand")
			  (vec_duplicate:VWIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vnshift><mode>3_nv_scalar_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr")
	(unspec:VWIMODES
	  [(truncate:VWIMODES
	     (any_shiftrt:<VWMODE>
	       (match_operand:<VWMODE> 1 "register_operand" "vr")
	       (vec_duplicate:VWIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vn<insn>.vx\t%0,%1,%2" : "vn<insn>.wx\t%0,%1,%2"; }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<vnshift><mode>3_nv_mask"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(if_then_else:VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VWIMODES
			  (any_shiftrt:<VWMODE>
			    (match_operand:<VWMODE> 3 "register_operand")
			    (match_operand:VWIMODES 4 "vector_shift_operand")))
			(match_operand:VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vnshift><mode>3_nv_mask_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VWIMODES
	  [(if_then_else:VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (truncate:VWIMODES
	       (any_shiftrt:<VWMODE>
		 (match_operand:<VWMODE> 3 "register_operand" "vr,vr")
		 (match_operand:VWIMODES 4 "vector_shift_operand" "vr,vk")))
	     (match_operand:VWIMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  {
    switch (which_alternative)
      {
      case 0:
	return target_subset_version_p ("v", 0, 7) ? "vn<insn>.vv\t%0,%3,%4,%1.t" : "vn<insn>.wv\t%0,%3,%4,%1.t";
      case 1:
	return target_subset_version_p ("v", 0, 7) ? "vn<insn>.vi\t%0,%3,%v4,%1.t" : "vn<insn>.wi\t%0,%3,%v4,%1.t";
      default:
	gcc_unreachable ();
      }
  }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<vnshift><mode>3_nv_scalar_mask"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(if_then_else:VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VWIMODES
			  (any_shiftrt:<VWMODE>
			    (match_operand:<VWMODE> 3 "register_operand")
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<vnshift><mode>3_scalar_nv_mask_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr")
	(unspec:VWIMODES
	  [(if_then_else:VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (truncate:VWIMODES
	       (any_shiftrt:<VWMODE>
		 (match_operand:<VWMODE> 3 "register_operand" "vr")
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
	     (match_operand:VWIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vn<insn>.vx\t%0,%3,%4,%1.t" : "vn<insn>.wx\t%0,%3,%4,%1.t"; }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vncvt<mode>"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(truncate:VWIMODES
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vncvt<mode>_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr")
	(unspec:VWIMODES
	  [(truncate:VWIMODES
	       (match_operand:<VWMODE> 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vnsrl.vx\t%0,%1,x0" : "vncvt.x.x.w\t%0,%1"; }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vncvt<mode>_mask"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(if_then_else:VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(truncate:VWIMODES
			  (match_operand:<VWMODE> 3 "register_operand"))
			(match_operand:VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vncvt<mode>3_mask_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VWIMODES
	  [(if_then_else:VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (truncate:VWIMODES
	       (match_operand:<VWMODE> 3 "register_operand" "vr,vr"))
	     (match_operand:VWIMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vnsrl.vx\t%0,%3,x0,%1.t" : "vncvt.x.x.w\t%0,%3,%1.t"; }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Min and Max instructions

(define_expand "<minmax><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_minmax:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<minmax><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(any_minmax:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (match_operand:VIMODES 2 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vv\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<minmax><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_minmax:VIMODES
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand"))
			(match_operand:VIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<minmax><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(any_minmax:VIMODES
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	     (match_operand:VIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<minmax><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_minmax:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<minmax><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_minmax:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (match_operand:VIMODES 4 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<minmax><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_minmax:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand"))
			  (match_operand:VIMODES 3 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<minmax><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_minmax:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r"))
	       (match_operand:VIMODES 3 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Integer divide instructions.

(define_expand "<optab><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_div:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(any_div:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (match_operand:VIMODES 2 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vv\t%0,%1,%2"
  [(set_attr "type" "vdiv")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(any_div:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(vec_duplicate:VIMODES
			  (match_operand:<VSUBMODE> 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

;; XXX: No divide instruction with vector-scalar,
;; all divide patterns prefer vector-vector.

(define_insn "*<optab><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(any_div:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "vr")
	     (vec_duplicate:VIMODES
	       (match_operand:<VSUBMODE> 2 "register_operand" "r")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%1,%2"
  [(set_attr "type" "vdiv")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_div:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (match_operand:VIMODES 4 "register_operand"))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_div:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (match_operand:VIMODES 4 "register_operand" "vr"))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vdiv")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<optab><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_div:VIMODES
			  (match_operand:VIMODES 3 "register_operand")
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 4 "register_operand")))
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<optab><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_div:VIMODES
	       (match_operand:VIMODES 3 "register_operand" "vr")
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 4 "register_operand" "r")))
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<insn>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vdiv")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector widening signed integer multiply
;; and unsigned integer multiply

(define_expand "wmul<u><mode>"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(mult:<VWMODE>
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 1 "register_operand"))
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmul<u><mode>_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(mult:<VWMODE>
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 1 "register_operand" "vr"))
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmul<u>.vv\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wmul<u><mode>_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(mult:<VWMODE>
			(any_extend:<VWMODE>
			  (match_operand:VWIMODES 1 "register_operand"))
			(any_extend:<VWMODE>
			  (vec_duplicate:VWIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmul<u><mode>_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(mult:<VWMODE>
	     (any_extend:<VWMODE>
	       (match_operand:VWIMODES 1 "register_operand" "vr"))
	     (any_extend:<VWMODE>
	       (vec_duplicate:VWIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmul<u>.vx\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wmul<u><mode>_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:<VWMODE>
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand"))
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmul<u><mode>_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:<VWMODE>
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 3 "register_operand" "vr"))
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmul<u>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wmul<u><mode>_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:<VWMODE>
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand"))
			  (any_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			  (match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmul<u><mode>_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:<VWMODE>
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 3 "register_operand" "vr"))
	       (any_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmul<u>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Vector widening signed-unsigned integer multiply

(define_expand "wmulsu<mode>"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(mult:<VWMODE>
			(sign_extend:<VWMODE>
			  (match_operand:VWIMODES 1 "register_operand"))
			(zero_extend:<VWMODE>
			  (match_operand:VWIMODES 2 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmulsu<mode>_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(mult:<VWMODE>
	     (sign_extend:<VWMODE>
	       (match_operand:VWIMODES 1 "register_operand" "vr"))
	     (zero_extend:<VWMODE>
	       (match_operand:VWIMODES 2 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmulsu.vv\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wmulsu<mode>_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(mult:<VWMODE>
			(sign_extend:<VWMODE>
			  (match_operand:VWIMODES 1 "register_operand"))
			(zero_extend:<VWMODE>
			  (vec_duplicate:VWIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmulsu<mode>_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(mult:<VWMODE>
	     (sign_extend:<VWMODE>
	       (match_operand:VWIMODES 1 "register_operand" "vr"))
	     (zero_extend:<VWMODE>
	       (vec_duplicate:VWIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmulsu.vx\t%0,%1,%2"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wmulsu<mode>_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:<VWMODE>
			  (sign_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand"))
			  (zero_extend:<VWMODE>
			    (match_operand:VWIMODES 4 "register_operand")))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmulsu<mode>_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:<VWMODE>
	       (sign_extend:<VWMODE>
		 (match_operand:VWIMODES 3 "register_operand" "vr"))
	       (zero_extend:<VWMODE>
		 (match_operand:VWIMODES 4 "register_operand" "vr")))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmulsu.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "wmulsu<mode>_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(mult:<VWMODE>
			  (sign_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand"))
			  (zero_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 4 "register_operand"))))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*wmulsu<mode>_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (mult:<VWMODE>
	       (sign_extend:<VWMODE>
		 (match_operand:VWIMODES 3 "register_operand" "vr"))
	       (zero_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 4 "register_operand" "r"))))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmulsu.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmul")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Single-Width Integer Multiply-Add Instructions

(define_expand "v<vmadd_sub><mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(add_sub:VIMODES
			(match_operand:VIMODES 3 "register_operand")
			(mult:VIMODES
			  (match_operand:VIMODES 2 "register_operand")
			  (match_operand:VIMODES 1 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*v<vmadd_sub><mode>_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(add_sub:VIMODES
	     (match_operand:VIMODES 3 "register_operand" "vr")
	     (mult:VIMODES
	       (match_operand:VIMODES 2 "register_operand" "vr")
	       (match_operand:VIMODES 1 "register_operand" "0")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<vmadd_sub>.vv\t%0,%2,%3"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "v<vmac><mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(add_sub:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(mult:VIMODES
			  (match_operand:VIMODES 2 "register_operand")
			  (match_operand:VIMODES 3 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_VMACS))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*v<vmac><mode>_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(add_sub:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "0")
	     (mult:VIMODES
	       (match_operand:VIMODES 2 "register_operand" "vr")
	       (match_operand:VIMODES 3 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_VMACS))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<vmac>.vv\t%0,%2,%3"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "v<vmadd_sub><mode>_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(add_sub:VIMODES
			(match_operand:VIMODES 3 "register_operand")
			(mult:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))
			  (match_operand:VIMODES 1 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*v<vmadd_sub><mode>_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(add_sub:VIMODES
	     (match_operand:VIMODES 3 "register_operand" "vr")
	     (mult:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	       (match_operand:VIMODES 1 "register_operand" "0")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<vmadd_sub>.vx\t%0,%2,%3"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "v<vmac><mode>_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(add_sub:VIMODES
			(match_operand:VIMODES 1 "register_operand")
			(mult:VIMODES
			  (vec_duplicate:VIMODES
			    (match_operand:<VSUBMODE> 2 "register_operand"))
			  (match_operand:VIMODES 3 "register_operand")))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_VMACS))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*v<vmac><mode>_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(add_sub:VIMODES
	     (match_operand:VIMODES 1 "register_operand" "0")
	     (mult:VIMODES
	       (vec_duplicate:VIMODES
		 (match_operand:<VSUBMODE> 2 "register_operand" "r"))
	       (match_operand:VIMODES 3 "register_operand" "vr")))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_VMACS))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "v<vmac>.vx\t%0,%2,%3"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<imac><mode>_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (match_operand:VIMODES 3 "register_operand")
			 (match_operand:VIMODES 4 "register_operand")]
		       UNSPEC_MASK_VMACS)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<imac><mode>_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VIMODES 2 "register_operand" "0")
	      (match_operand:VIMODES 3 "register_operand" "vr")
	      (match_operand:VIMODES 4 "register_operand" "vr")]
	    UNSPEC_MASK_VMACS)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<imac>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])


(define_expand "<imac><mode>_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (vec_duplicate:VIMODES
			   (match_operand:<VSUBMODE> 3 "register_operand"))
			 (match_operand:VIMODES 4 "register_operand")]
		       UNSPEC_MASK_VMACS)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<imac><mode>_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VIMODES 2 "register_operand" "0")
	      (vec_duplicate:VIMODES
		(match_operand:<VSUBMODE> 3 "register_operand" "r"))
	      (match_operand:VIMODES 4 "register_operand" "vr")]
	    UNSPEC_MASK_VMACS)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<imac>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Widening Integer Multiply-Add Instructions

(define_expand "<u>madd<mode><vwmode>4"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(plus:<VWMODE>
			(mult:<VWMODE>
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 2 "register_operand"))
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand")))
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vwmode>4_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(plus:<VWMODE>
	     (mult:<VWMODE>
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 1 "register_operand" "vr"))
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmacc<u>.vv\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vwmode>4"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(plus:<VWMODE>
			(mult:<VWMODE>
			  (sign_extend:<VWMODE>
			    (match_operand:VWIMODES 2 "register_operand"))
			  (zero_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand")))
			 (match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vwmode>4_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(plus:<VWMODE>
	     (mult:<VWMODE>
	       (sign_extend:<VWMODE>
		 (match_operand:VWIMODES 1 "register_operand" "vr"))
	       (zero_extend:<VWMODE>
		 (match_operand:VWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmaccsu.vv\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<u>madd<mode><vwmode>4_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(plus:<VWMODE>
			(mult:<VWMODE>
			  (any_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (any_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand")))
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vwmode>4_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(plus:<VWMODE>
	     (mult:<VWMODE>
	       (any_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "r")))
	       (any_extend:<VWMODE>
		 (match_operand:VWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmacc<u>.vx\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vwmode>4_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(plus:<VWMODE>
			(mult:<VWMODE>
			  (sign_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (zero_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand")))
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vwmode>4_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(plus:<VWMODE>
	     (mult:<VWMODE>
	       (sign_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "r")))
	       (zero_extend:<VWMODE>
		 (match_operand:VWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmaccsu.vx\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "usmadd<mode><vwmode>4_scalar"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(plus:<VWMODE>
			(mult:<VWMODE>
			  (zero_extend:<VWMODE>
			    (vec_duplicate:VWIMODES
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (sign_extend:<VWMODE>
			    (match_operand:VWIMODES 3 "register_operand")))
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*usmadd<mode><vwmode>4_scalar_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(plus:<VWMODE>
	     (mult:<VWMODE>
	       (zero_extend:<VWMODE>
		 (vec_duplicate:VWIMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "r")))
	       (sign_extend:<VWMODE>
		 (match_operand:VWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmaccus.vx\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<u>madd<mode><vwmode>4_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VWMODE>
			   (mult:<VWMODE>
			     (any_extend:<VWMODE>
			       (match_operand:VWIMODES 3 "register_operand"))
			     (any_extend:<VWMODE>
			       (match_operand:VWIMODES 4 "register_operand")))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_VMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vwmode>4_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VWMODE>
		(mult:<VWMODE>
		  (any_extend:<VWMODE>
		    (match_operand:VWIMODES 3 "register_operand" "vr"))
		  (any_extend:<VWMODE>
		    (match_operand:VWIMODES 4 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmacc<u>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vwmode>4_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VWMODE>
			   (mult:<VWMODE>
			     (sign_extend:<VWMODE>
			       (match_operand:VWIMODES 3 "register_operand"))
			     (zero_extend:<VWMODE>
			       (match_operand:VWIMODES 4 "register_operand")))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_VMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vwmode>4_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VWMODE>
		(mult:<VWMODE>
		  (sign_extend:<VWMODE>
		    (match_operand:VWIMODES 3 "register_operand" "vr"))
		  (zero_extend:<VWMODE>
		    (match_operand:VWIMODES 4 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmaccsu.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<u>madd<mode><vwmode>4_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VWMODE>
			   (mult:<VWMODE>
			     (any_extend:<VWMODE>
			       (vec_duplicate:VWIMODES
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			     (any_extend:<VWMODE>
			       (match_operand:VWIMODES 4 "register_operand")))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_VMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vwmode>4_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VWMODE>
		(mult:<VWMODE>
		  (any_extend:<VWMODE>
		    (vec_duplicate:VWIMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "r")))
		  (any_extend:<VWMODE>
		    (match_operand:VWIMODES 4 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmacc<u>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vwmode>4_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VWMODE>
			   (mult:<VWMODE>
			     (sign_extend:<VWMODE>
			       (vec_duplicate:VWIMODES
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			     (zero_extend:<VWMODE>
			       (match_operand:VWIMODES 4 "register_operand")))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_VMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vwmode>4_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VWMODE>
		(mult:<VWMODE>
		  (sign_extend:<VWMODE>
		    (vec_duplicate:VWIMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "r")))
		  (zero_extend:<VWMODE>
		    (match_operand:VWIMODES 4 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmaccsu.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "usmadd<mode><vwmode>4_scalar_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(unspec:<VWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VWMODE>
			   (mult:<VWMODE>
			     (zero_extend:<VWMODE>
			       (vec_duplicate:VWIMODES
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			     (sign_extend:<VWMODE>
			       (match_operand:VWIMODES 4 "register_operand")))
			   (match_operand:<VWMODE> 2 "register_operand"))]
		       UNSPEC_VMADD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*usmadd<mode><vwmode>4_scalar_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(unspec:<VWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VWMODE>
		(mult:<VWMODE>
		  (zero_extend:<VWMODE>
		    (vec_duplicate:VWIMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "r")))
		  (sign_extend:<VWMODE>
		    (match_operand:VWIMODES 4 "register_operand" "vr")))
		(match_operand:<VWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VMADD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vwmaccus.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Quad-Widening Integer Multiply-Add Instructions

(define_expand "<u>madd<mode><vqwmode>4"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(plus:<VQWMODE>
			(mult:<VQWMODE>
			  (any_extend:<VQWMODE>
			    (match_operand:VQWIMODES 2 "register_operand"))
			  (any_extend:<VQWMODE>
			    (match_operand:VQWIMODES 3 "register_operand")))
			(match_operand:<VQWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vqwmode>4_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(plus:<VQWMODE>
	     (mult:<VQWMODE>
	       (any_extend:<VQWMODE>
		 (match_operand:VQWIMODES 1 "register_operand" "vr"))
	       (any_extend:<VQWMODE>
		 (match_operand:VQWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VQWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmacc<u>.vv\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vqwmode>4"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(plus:<VQWMODE>
			(mult:<VQWMODE>
			  (sign_extend:<VQWMODE>
			    (match_operand:VQWIMODES 2 "register_operand"))
			  (zero_extend:<VQWMODE>
			    (match_operand:VQWIMODES 3 "register_operand")))
			(match_operand:<VQWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vqwmode>4_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(plus:<VQWMODE>
	     (mult:<VQWMODE>
	       (sign_extend:<VQWMODE>
		 (match_operand:VQWIMODES 1 "register_operand" "vr"))
	       (zero_extend:<VQWMODE>
		 (match_operand:VQWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VQWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmaccsu.vv\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<u>madd<mode><vqwmode>4_scalar"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(plus:<VQWMODE>
			(mult:<VQWMODE>
			  (any_extend:<VQWMODE>
			    (vec_duplicate:VQWIMODES
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (any_extend:<VQWMODE>
			    (match_operand:VQWIMODES 3 "register_operand")))
			(match_operand:<VQWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vqwmode>4_scalar_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(plus:<VQWMODE>
	     (mult:<VQWMODE>
	       (any_extend:<VQWMODE>
		 (vec_duplicate:VQWIMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "r")))
	       (any_extend:<VQWMODE>
		 (match_operand:VQWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VQWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmacc<u>.vx\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vqwmode>4_scalar"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(plus:<VQWMODE>
			(mult:<VQWMODE>
			  (sign_extend:<VQWMODE>
			    (vec_duplicate:VQWIMODES
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (zero_extend:<VQWMODE>
			    (match_operand:VQWIMODES 3 "register_operand")))
			(match_operand:<VQWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vqwmode>4_scalar_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(plus:<VQWMODE>
	     (mult:<VQWMODE>
	       (sign_extend:<VQWMODE>
		 (vec_duplicate:VQWIMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "r")))
		 (zero_extend:<VQWMODE>
		   (match_operand:VQWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VQWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmaccsu.vx\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "usmadd<mode><vqwmode>4_scalar"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(plus:<VQWMODE>
			(mult:<VQWMODE>
			  (zero_extend:<VQWMODE>
			    (vec_duplicate:VQWIMODES
			      (match_operand:<VSUBMODE> 2 "register_operand")))
			  (sign_extend:<VQWMODE>
			    (match_operand:VQWIMODES 3 "register_operand")))
			(match_operand:<VQWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*usmadd<mode><vqwmode>4_scalar_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(plus:<VQWMODE>
	     (mult:<VQWMODE>
	       (zero_extend:<VQWMODE>
		 (vec_duplicate:VQWIMODES
		   (match_operand:<VSUBMODE> 1 "register_operand" "r")))
	       (sign_extend:<VQWMODE>
		 (match_operand:VQWIMODES 2 "register_operand" "vr")))
	     (match_operand:<VQWMODE> 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmaccus.vx\t%0,%1,%2"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<u>madd<mode><vqwmode>4_mask"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		    [(unspec:<VQWMODE>
		       [(match_operand:<VCMPEQUIV> 1 "register_operand")
			(plus:<VQWMODE>
			  (mult:<VQWMODE>
			    (any_extend:<VQWMODE>
			      (match_operand:VQWIMODES 3 "register_operand"))
			    (any_extend:<VQWMODE>
			      (match_operand:VQWIMODES 4 "register_operand")))
			(match_operand:<VQWMODE> 2 "register_operand"))]
		       UNSPEC_VQMAC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vqwmode>4_mask_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(unspec:<VQWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VQWMODE>
		(mult:<VQWMODE>
		  (any_extend:<VQWMODE>
		    (match_operand:VQWIMODES 3 "register_operand" "vr"))
		  (any_extend:<VQWMODE>
		    (match_operand:VQWIMODES 4 "register_operand" "vr")))
		(match_operand:<VQWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VQMAC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmacc<u>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vqwmode>4_mask"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(unspec:<VQWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VQWMODE>
			   (mult:<VQWMODE>
			     (sign_extend:<VQWMODE>
			       (match_operand:VQWIMODES 3 "register_operand"))
			     (zero_extend:<VQWMODE>
			       (match_operand:VQWIMODES 4 "register_operand")))
			   (match_operand:<VQWMODE> 2 "register_operand"))]
		       UNSPEC_VQMAC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vqwmode>4_mask_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(unspec:<VQWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VQWMODE>
		(mult:<VQWMODE>
		  (sign_extend:<VQWMODE>
		    (match_operand:VQWIMODES 3 "register_operand" "vr"))
		  (zero_extend:<VQWMODE>
		    (match_operand:VQWIMODES 4 "register_operand" "vr")))
		(match_operand:<VQWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VQMAC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmaccsu.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<u>madd<mode><vqwmode>4_scalar_mask"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(unspec:<VQWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VQWMODE>
			   (mult:<VQWMODE>
			     (any_extend:<VQWMODE>
			       (vec_duplicate:VQWIMODES
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			     (any_extend:<VQWMODE>
			       (match_operand:VQWIMODES 4 "register_operand")))
			   (match_operand:<VQWMODE> 2 "register_operand"))]
		       UNSPEC_VQMAC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<u>madd<mode><vqwmode>4_scalar_mask_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(unspec:<VQWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VQWMODE>
		(mult:<VQWMODE>
		  (any_extend:<VQWMODE>
		    (vec_duplicate:VQWIMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "r")))
		  (any_extend:<VQWMODE>
		    (match_operand:VQWIMODES 4 "register_operand" "vr")))
		(match_operand:<VQWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VQMAC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmacc<u>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "sumadd<mode><vqwmode>4_scalar_mask"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(unspec:<VQWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VQWMODE>
			   (mult:<VQWMODE>
			     (sign_extend:<VQWMODE>
			       (vec_duplicate:VQWIMODES
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			     (zero_extend:<VQWMODE>
			       (match_operand:VQWIMODES 4 "register_operand")))
			   (match_operand:<VQWMODE> 2 "register_operand"))]
		       UNSPEC_VQMAC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*sumadd<mode><vqwmode>4_scalar_mask_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(unspec:<VQWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VQWMODE>
		(mult:<VQWMODE>
		  (sign_extend:<VQWMODE>
		    (vec_duplicate:VQWIMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "r")))
		  (zero_extend:<VQWMODE>
		    (match_operand:VQWIMODES 4 "register_operand" "vr")))
		(match_operand:<VQWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VQMAC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmaccsu.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "usmadd<mode><vqwmode>4_scalar_mask"
  [(parallel [(set (match_operand:<VQWMODE> 0 "register_operand")
		   (unspec:<VQWMODE>
		     [(unspec:<VQWMODE>
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (plus:<VQWMODE>
			   (mult:<VQWMODE>
			     (zero_extend:<VQWMODE>
			       (vec_duplicate:VQWIMODES
				 (match_operand:<VSUBMODE> 3 "register_operand")))
			     (sign_extend:<VQWMODE>
			       (match_operand:VQWIMODES 4 "register_operand")))
			   (match_operand:<VQWMODE> 2 "register_operand"))]
		       UNSPEC_VQMAC)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*usmadd<mode><vqwmode>4_scalar_mask_nosetvl"
  [(set (match_operand:<VQWMODE> 0 "register_operand" "=&vr")
	(unspec:<VQWMODE>
	  [(unspec:<VQWMODE>
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (plus:<VQWMODE>
		(mult:<VQWMODE>
		  (zero_extend:<VQWMODE>
		    (vec_duplicate:VQWIMODES
		      (match_operand:<VSUBMODE> 3 "register_operand" "r")))
		  (sign_extend:<VQWMODE>
		    (match_operand:VQWIMODES 4 "register_operand" "vr")))
		(match_operand:<VQWMODE> 2 "register_operand" "0"))]
	    UNSPEC_VQMAC)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vqmaccus.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmadd")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Single-Width Floating-Point/Integer Type-Convert Instructions

(define_expand "<fix_cvt><mode><vintequiv>2"
  [(parallel [(set (match_operand:<VINTEQUIV> 0 "register_operand")
		   (unspec:<VINTEQUIV>
		     [(any_fix:<VINTEQUIV>
			(match_operand:VFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fix_cvt><mode><vintequiv>2_nosetvl"
  [(set (match_operand:<VINTEQUIV> 0 "register_operand" "=vr")
	(unspec:<VINTEQUIV>
	  [(any_fix:<VINTEQUIV>
	     (match_operand:VFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfcvt.rtz.x<u>.f.v %0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fix_cvt><mode><vintequiv>2_mask"
  [(parallel [(set (match_operand:<VINTEQUIV> 0 "register_operand")
		   (unspec:<VINTEQUIV>
		     [(if_then_else:<VINTEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fix:<VINTEQUIV>
			  (match_operand:VFMODES 3 "register_operand"))
			(match_operand:<VINTEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fix_cvt><mode><vintequiv>2_mask_nosetvl"
  [(set (match_operand:<VINTEQUIV> 0 "register_operand" "=vr")
	(unspec:<VINTEQUIV>
	  [(if_then_else:<VINTEQUIV>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fix:<VINTEQUIV>
	       (match_operand:VFMODES 3 "register_operand" "vr"))
	     (match_operand:<VINTEQUIV> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfcvt.rtz.x<u>.f.v %0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fcvt_xf><mode><vintequiv>2"
  [(parallel [(set (match_operand:<VINTEQUIV> 0 "register_operand")
		   (unspec:<VINTEQUIV>
		     [(unspec:<VINTEQUIV>
			[(match_operand:VFMODES 1 "register_operand")]
		       UNSPEC_FCVT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fcvt_xf><mode><vintequiv>2_nosetvl"
  [(set (match_operand:<VINTEQUIV> 0 "register_operand" "=vr")
	(unspec:<VINTEQUIV>
	  [(unspec:<VINTEQUIV>
	     [(match_operand:VFMODES 1 "register_operand" "vr")]
	   UNSPEC_FCVT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfcvt.x<fu>.f.v %0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fcvt_xf><mode><vintequiv>2_mask"
  [(parallel [(set (match_operand:<VINTEQUIV> 0 "register_operand")
		   (unspec:<VINTEQUIV>
		     [(if_then_else:<VINTEQUIV>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:<VINTEQUIV>
			  [(match_operand:VFMODES 3 "register_operand")]
			 UNSPEC_FCVT)
			(match_operand:<VINTEQUIV> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fcvt_xf><mode><vintequiv>2_mask_nosetvl"
  [(set (match_operand:<VINTEQUIV> 0 "register_operand" "=vr")
	(unspec:<VINTEQUIV>
	  [(if_then_else:<VINTEQUIV>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:<VINTEQUIV>
	       [(match_operand:VFMODES 3 "register_operand" "vr")]
	      UNSPEC_FCVT)
	     (match_operand:<VINTEQUIV> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfcvt.x<fu>.f.v %0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<float_cvt><vintequiv><mode>2"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(any_float:VFMODES
			(match_operand:<VINTEQUIV> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<float_cvt><vintequiv><mode>2_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(any_float:VFMODES
	     (match_operand:<VINTEQUIV> 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfcvt.f.x<u>.v\t%0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<float_cvt><vintequiv><mode>2_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_float:VFMODES
			  (match_operand:<VINTEQUIV> 3 "register_operand"))
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<float_cvt><vintequiv><mode>2_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_float:VFMODES
	       (match_operand:<VINTEQUIV> 3 "register_operand"  "vr"))
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfcvt.f.x<u>.v\t%0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Widening Floating-Point/Integer Type-Convert Instructions

(define_expand "<fix_cvt><mode><vfwimode>2"
  [(parallel [(set (match_operand:<VFWIMODE> 0 "register_operand")
		   (unspec:<VFWIMODE>
		     [(any_fix:<VFWIMODE>
			(match_operand:VWFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fix_cvt><mode><vfwimode>2_nosetvl"
  [(set (match_operand:<VFWIMODE> 0 "register_operand" "=&vr")
	(unspec:<VFWIMODE>
	  [(any_fix:<VFWIMODE>
	     (match_operand:VWFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.rtz.x<u>.f.v %0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fix_cvt><mode><vfwimode>2_mask"
  [(parallel [(set (match_operand:<VFWIMODE> 0 "register_operand")
		   (unspec:<VFWIMODE>
		     [(if_then_else:<VFWIMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fix:<VFWIMODE>
			  (match_operand:VWFMODES 3 "register_operand"))
			(match_operand:<VFWIMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fix_cvt><mode><vfwimode>2_mask_nosetvl"
  [(set (match_operand:<VFWIMODE> 0 "register_operand" "=&vr")
	(unspec:<VFWIMODE>
	  [(if_then_else:<VFWIMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fix:<VFWIMODE>
	       (match_operand:VWFMODES 3 "register_operand" "vr"))
	     (match_operand:<VFWIMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.rtz.x<u>.f.v %0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fcvt_xf><mode><vfwimode>2"
  [(parallel [(set (match_operand:<VFWIMODE> 0 "register_operand")
		   (unspec:<VFWIMODE>
		     [(unspec:<VFWIMODE>
			[(match_operand:VWFMODES 1 "register_operand")]
		       UNSPEC_FCVT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fcvt_xf><mode><vfwimode>2_nosetvl"
  [(set (match_operand:<VFWIMODE> 0 "register_operand" "=&vr")
	(unspec:<VFWIMODE>
	  [(unspec:<VFWIMODE>
	     [(match_operand:VWFMODES 1 "register_operand" "vr")]
	    UNSPEC_FCVT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.x<fu>.f.v %0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fcvt_xf><mode><vfwimode>2_mask"
  [(parallel [(set (match_operand:<VFWIMODE> 0 "register_operand")
		   (unspec:<VFWIMODE>
		     [(if_then_else:<VFWIMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:<VFWIMODE>
			  [(match_operand:VWFMODES 3 "register_operand")]
			 UNSPEC_FCVT)
			(match_operand:<VFWIMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fcvt_xf><mode><vfwimode>2_mask_nosetvl"
  [(set (match_operand:<VFWIMODE> 0 "register_operand" "=&vr")
	(unspec:<VFWIMODE>
	  [(if_then_else:<VFWIMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:<VFWIMODE>
	       [(match_operand:VWFMODES 3 "register_operand" "vr")]
	      UNSPEC_FCVT)
	     (match_operand:<VFWIMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.x<fu>.f.v %0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<float_cvt><mode><viwfmode>2"
  [(parallel [(set (match_operand:<VIWFMODE> 0 "register_operand")
		   (unspec:<VIWFMODE>
		     [(any_float:<VIWFMODE>
			(match_operand:FCVT_VWIMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<float_cvt><mode><viwfmode>2_nosetvl"
  [(set (match_operand:<VIWFMODE> 0 "register_operand" "=&vr")
	(unspec:<VIWFMODE>
	  [(any_float:<VIWFMODE>
	     (match_operand:FCVT_VWIMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.f.x<u>.v\t%0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<float_cvt><mode><viwfmode>2_mask"
  [(parallel [(set (match_operand:<VIWFMODE> 0 "register_operand")
		   (unspec:<VIWFMODE>
		     [(if_then_else:<VIWFMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_float:<VIWFMODE>
			  (match_operand:FCVT_VWIMODES 3 "register_operand"))
			(match_operand:<VIWFMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<float_cvt><mode><viwfmode>2_mask_nosetvl"
  [(set (match_operand:<VIWFMODE> 0 "register_operand" "=&vr")
	(unspec:<VIWFMODE>
	  [(if_then_else:<VIWFMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_float:<VIWFMODE>
	       (match_operand:FCVT_VWIMODES 3 "register_operand" "vr"))
	     (match_operand:<VIWFMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.f.x<u>.v\t%0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "extend<mode><vwmode>2"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(float_extend:<VWMODE>
			(match_operand:VWFMODES 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*extend<mode><vwmode>2_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(float_extend:<VWMODE>
	     (match_operand:VWFMODES 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.f.f.v\t%0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "extend<mode><vwmode>2_mask"
  [(parallel [(set (match_operand:<VWMODE> 0 "register_operand")
		   (unspec:<VWMODE>
		     [(if_then_else:<VWMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(float_extend:<VWMODE>
			  (match_operand:VWFMODES 3 "register_operand"))
			(match_operand:<VWMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*extend<mode><vwmode>2_mask_nosetvl"
  [(set (match_operand:<VWMODE> 0 "register_operand" "=&vr")
	(unspec:<VWMODE>
	  [(if_then_else:<VWMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (float_extend:<VWMODE>
	       (match_operand:VWFMODES 3 "register_operand" "vr"))
	     (match_operand:<VWMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfwcvt.f.f.v\t%0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Narrowing Floating-Point/Integer Type-Convert Instructions

(define_expand "<fix_cvt><viwfmode><mode>2"
  [(parallel [(set (match_operand:FCVT_VWIMODES 0 "register_operand")
		   (unspec:FCVT_VWIMODES
		     [(any_fix:FCVT_VWIMODES
			(match_operand:<VIWFMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fix_cvt><viwfmode><mode>2_nosetvl"
  [(set (match_operand:FCVT_VWIMODES 0 "register_operand" "=&vr")
	(unspec:FCVT_VWIMODES
	  [(any_fix:FCVT_VWIMODES
	     (match_operand:<VIWFMODE> 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfncvt.rtz.x<u>.f.w %0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fix_cvt><viwfmode><mode>2_mask"
  [(parallel [(set (match_operand:FCVT_VWIMODES 0 "register_operand")
		   (unspec:FCVT_VWIMODES
		     [(if_then_else:FCVT_VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_fix:FCVT_VWIMODES
			  (match_operand:<VIWFMODE> 3 "register_operand"))
			(match_operand:FCVT_VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fix_cvt><viwfmode><mode>2_mask_nosetvl"
  [(set (match_operand:FCVT_VWIMODES 0 "register_operand" "=&vr")
	(unspec:FCVT_VWIMODES
	  [(if_then_else:FCVT_VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_fix:FCVT_VWIMODES
	       (match_operand:<VIWFMODE> 3 "register_operand" "vr"))
	     (match_operand:FCVT_VWIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfncvt.rtz.x<u>.f.w %0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fcvt_xf><viwfmode><mode>2"
  [(parallel [(set (match_operand:FCVT_VWIMODES 0 "register_operand")
		   (unspec:FCVT_VWIMODES
		     [(unspec:FCVT_VWIMODES
			[(match_operand:<VIWFMODE> 1 "register_operand")]
		       UNSPEC_FCVT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fcvt_xf><viwfmode><mode>2_nosetvl"
  [(set (match_operand:FCVT_VWIMODES 0 "register_operand" "=&vr")
	(unspec:FCVT_VWIMODES
	  [(unspec:FCVT_VWIMODES
	     [(match_operand:<VIWFMODE> 1 "register_operand" "vr")]
	    UNSPEC_FCVT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfncvt.x<fu>.f.v %0,%1" : "vfncvt.x<fu>.f.w %0,%1"; }
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<fcvt_xf><viwfmode><mode>2_mask"
  [(parallel [(set (match_operand:FCVT_VWIMODES 0 "register_operand")
		   (unspec:FCVT_VWIMODES
		     [(if_then_else:FCVT_VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:FCVT_VWIMODES
			  [(match_operand:<VIWFMODE> 3 "register_operand")]
			 UNSPEC_FCVT)
		       (match_operand:FCVT_VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<fcvt_xf><viwfmode><mode>2_mask_nosetvl"
  [(set (match_operand:FCVT_VWIMODES 0 "register_operand" "=&vr")
	(unspec:FCVT_VWIMODES
	  [(if_then_else:FCVT_VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:FCVT_VWIMODES
	       [(match_operand:<VIWFMODE> 3 "register_operand" "vr")]
	      UNSPEC_FCVT)
	     (match_operand:FCVT_VWIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfncvt.x<fu>.f.v %0,%3,%1.t" : "vfncvt.x<fu>.f.w %0,%3,%1.t"; }
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<float_cvt><vfwimode><mode>2"
  [(parallel [(set (match_operand:VWFMODES 0 "register_operand")
		   (unspec:VWFMODES
		     [(any_float:VWFMODES
			(match_operand:<VFWIMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<float_cvt><vfwimode><mode>2_nosetvl"
  [(set (match_operand:VWFMODES 0 "register_operand" "=&vr")
	(unspec:VWFMODES
	  [(any_float:VWFMODES
	     (match_operand:<VFWIMODE> 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfncvt.f.x<u>.v\t%0,%1" : "vfncvt.f.x<u>.w\t%0,%1"; }
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<float_cvt><vfwimode><mode>2_mask"
  [(parallel [(set (match_operand:VWFMODES 0 "register_operand")
		   (unspec:VWFMODES
		     [(if_then_else:VWFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(any_float:VWFMODES
			  (match_operand:<VFWIMODE> 3 "register_operand"))
			(match_operand:VWFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*<float_cvt><vfwimode><mode>2_mask_nosetvl"
  [(set (match_operand:VWFMODES 0 "register_operand" "=&vr")
	(unspec:VWFMODES
	  [(if_then_else:VWFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (any_float:VWFMODES
	       (match_operand:<VFWIMODE> 3 "register_operand" "vr"))
	     (match_operand:VWFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfncvt.f.x<u>.v\t%0,%3,%1.t" : "vfncvt.f.x<u>.w\t%0,%3,%1.t"; }
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "trunc<vwmode><mode>2"
  [(parallel [(set (match_operand:VWFMODES 0 "register_operand")
		   (unspec:VWFMODES
		     [(float_truncate:VWFMODES
			(match_operand:<VWMODE> 1 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*trunc<vwmode><mode>2_nosetvl"
  [(set (match_operand:VWFMODES 0 "register_operand" "=&vr")
	(unspec:VWFMODES
	  [(float_truncate:VWFMODES
	     (match_operand:<VWMODE> 1 "register_operand" "vr"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfncvt.f.f.v\t%0,%1" : "vfncvt.f.f.w\t%0,%1"; }
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "trunc<vwmode><mode>2_mask"
  [(parallel [(set (match_operand:VWFMODES 0 "register_operand")
		   (unspec:VWFMODES
		     [(if_then_else:VWFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(float_truncate:VWFMODES
			  (match_operand:<VWMODE> 3 "register_operand"))
			(match_operand:VWFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*trunc<vwmode><mode>2_mask_nosetvl"
  [(set (match_operand:VWFMODES 0 "register_operand" "=&vr")
	(unspec:VWFMODES
	  [(if_then_else:VWFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (float_truncate:VWFMODES
	       (match_operand:<VWMODE> 3 "register_operand" "vr"))
	     (match_operand:VWFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  { return target_subset_version_p ("v", 0, 7) ? "vfncvt.f.f.v\t%0,%3,%1.t" : "vfncvt.f.f.w\t%0,%3,%1.t"; }
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "trunc_rod<vwmode><mode>2"
  [(parallel [(set (match_operand:VWFMODES 0 "register_operand")
		   (unspec:VWFMODES
		     [(unspec:VWFMODES
			[(match_operand:<VWMODE> 1 "register_operand")]
		       UNSPEC_ROD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*trunc_rod<vwmode><mode>2_nosetvl"
  [(set (match_operand:VWFMODES 0 "register_operand" "=&vr")
	(unspec:VWFMODES
	  [(unspec:VWFMODES
	     [(match_operand:<VWMODE> 1 "register_operand" "vr")]
	    UNSPEC_ROD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfncvt.rod.f.f.w\t%0,%1"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "trunc_rod<vwmode><mode>2_mask"
  [(parallel [(set (match_operand:VWFMODES 0 "register_operand")
		   (unspec:VWFMODES
		     [(if_then_else:VWFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VWFMODES
			  [(match_operand:<VWMODE> 3 "register_operand")]
			 UNSPEC_ROD)
			(match_operand:VWFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*trunc_rod<vwmode><mode>2_mask_nosetvl"
  [(set (match_operand:VWFMODES 0 "register_operand" "=&vr")
	(unspec:VWFMODES
	  [(if_then_else:VWFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VWFMODES
	       [(match_operand:<VWMODE> 3 "register_operand" "vr")]
	      UNSPEC_ROD)
	     (match_operand:VWFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfncvt.rod.f.f.w\t%0,%3,%1.t"
  [(set_attr "type" "vfcvt")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Floating-Point Classify Instruction.

(define_expand "vfclass<mode>2"
  [(parallel [(set (match_operand:<VLMODE> 0 "register_operand")
		   (unspec:<VLMODE>
		     [(unspec:<VLMODE>
			[(match_operand:VFMODES 1 "register_operand")]
		       UNSPEC_VFCLASS)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfclass<mode>2_nosetvl"
  [(set (match_operand:<VLMODE> 0 "register_operand" "=&vr")
	(unspec:<VLMODE>
	  [(unspec:<VLMODE>
	     [(match_operand:VFMODES 1 "register_operand" "vr")]
	    UNSPEC_VFCLASS)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfclass.v\t%0,%1"
  [(set_attr "type" "vfclass")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfclass<mode>2_mask"
  [(parallel [(set (match_operand:<VLMODE> 0 "register_operand")
		   (unspec:<VLMODE>
		     [(if_then_else:<VLMODE>
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:<VLMODE>
			  [(match_operand:VFMODES 3 "register_operand")]
			 UNSPEC_VFCLASS)
			(match_operand:<VLMODE> 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfclass<mode>2_mask_nosetvl"
  [(set (match_operand:<VLMODE> 0 "register_operand" "=vr")
	(unspec:<VLMODE>
	  [(if_then_else:<VLMODE>
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:<VLMODE>
	       [(match_operand:VFMODES 3 "register_operand" "vr")]
	      UNSPEC_VFCLASS)
	     (match_operand:<VLMODE> 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfclass.v\t%0,%3,%1.t"
  [(set_attr "type" "vfclass")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Integer Slide Instructions.

(define_expand "vslide<ud><VMODES:mode><X:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:VMODES 1 "register_operand")
			 (match_operand:VMODES 2 "register_operand")
			 (match_operand:X 3 "reg_or_uimm5_operand")]
			UNSPEC_VSLIDES)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vslide<ud><VMODES:mode><X:mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr, &vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:VMODES 1 "register_operand" "0, 0")
	      (match_operand:VMODES 2 "register_operand" "vr, vr")
	      (match_operand:X 3 "reg_or_uimm5_operand" "r, K")]
	    UNSPEC_VSLIDES)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vslide<ud>.vx\t%0,%2,%3
   vslide<ud>.vi\t%0,%2,%3"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vslide<ud><VMODES:mode><X:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VMODES
			  [(match_operand:VMODES 3 "register_operand")
			   (match_operand:X 4 "reg_or_uimm5_operand")]
			 UNSPEC_VSLIDES)
			(match_operand:VMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vslide<ud><VMODES:mode><X:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (unspec:VMODES
	       [(match_operand:VMODES 3 "register_operand" "vr,vr")
		(match_operand:X 4 "reg_or_uimm5_operand" "r,K")]
	      UNSPEC_VSLIDES)
	     (match_operand:VMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vslide<ud>.vx\t%0,%3,%4,%1.t
   vslide<ud>.vi\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vslide1<ud><VIMODES:mode><X:mode>"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (match_operand:X 2 "register_operand")]
		       UNSPEC_VSLIDES1)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vslide1<ud><VIMODES:mode><X:mode>_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:VIMODES 1 "register_operand" "vr")
	      (match_operand:X 2 "register_operand" "r")]
	    UNSPEC_VSLIDES1)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vslide1<ud>.vx\t%0,%1,%2"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vslide1<ud><VIMODES:mode><X:mode>_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (match_operand:X 4 "register_operand")]
			 UNSPEC_VSLIDES1)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vslide1<ud><VIMODES:mode><X:mode>_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=&vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VIMODES
	       [(match_operand:VIMODES 3 "register_operand" "vr")
		(match_operand:X 4 "register_operand" "r")]
	      UNSPEC_VSLIDES1)
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vslide1<ud>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector FP Slide Instructions.

(define_expand "vfslide1<ud><mode>"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(unspec:VFMODES
			[(match_operand:VFMODES 1 "register_operand")
			 (match_operand:<VSUBMODE> 2 "register_operand")]
		       UNSPEC_VFSLIDES1)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfslide1<ud><mode>_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=&vr")
	(unspec:VFMODES
	  [(unspec:VFMODES
	     [(match_operand:VFMODES 1 "register_operand" "vr")
	      (match_operand:<VSUBMODE> 2 "register_operand" "f")]
	    UNSPEC_VFSLIDES1)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfslide1<ud>.vf\t%0,%1,%2"
  [(set_attr "type" "vfmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vfslide1<ud><mode>_mask"
  [(parallel [(set (match_operand:VFMODES 0 "register_operand")
		   (unspec:VFMODES
		     [(if_then_else:VFMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VFMODES
			  [(match_operand:VFMODES 3 "register_operand")
			   (match_operand:<VSUBMODE> 4 "register_operand")]
			 UNSPEC_VFSLIDES1)
			(match_operand:VFMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
{
})

(define_insn "*vfslide1<ud><mode>_mask_nosetvl"
  [(set (match_operand:VFMODES 0 "register_operand" "=&vr")
	(unspec:VFMODES
	  [(if_then_else:VFMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VFMODES
	       [(match_operand:VFMODES 3 "register_operand" "vr")
		(match_operand:<VSUBMODE> 4 "register_operand" "f")]
	      UNSPEC_VFSLIDES1)
	     (match_operand:VFMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR && TARGET_HARD_FLOAT"
  "vfslide1<ud>.vf\t%0,%3,%4,%1.t"
  [(set_attr "type" "vfmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Register Gather Instruction

(define_expand "vrgather<mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:VMODES 1 "register_operand")
			 (match_operand:<VLMODE> 2 "register_operand")]
		       UNSPEC_VRGATHER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vrgather<mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:VMODES 1 "register_operand" "vr")
	      (match_operand:<VLMODE> 2 "register_operand" "vr")]
	    UNSPEC_VRGATHER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrgather.vv\t%0,%1,%2"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vrgather<mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VMODES
			  [(match_operand:VMODES 3 "register_operand")
			   (match_operand:<VLMODE> 4 "register_operand")]
			 UNSPEC_VRGATHER)
			(match_operand:VMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vrgather<mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VMODES
	       [(match_operand:VMODES 3 "register_operand" "vr")
		(match_operand:<VLMODE> 4 "register_operand" "vr")]
	      UNSPEC_VRGATHER)
	     (match_operand:VMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrgather.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vrgatherei<VMODES:mode><VIMODES:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:VMODES 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")]
		       UNSPEC_VRGATHER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vrgatherei<VMODES:mode><VIMODES:mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:VMODES 1 "register_operand" "vr")
	      (match_operand:VIMODES 2 "register_operand" "vr")]
	    UNSPEC_VRGATHER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrgatherei<VIMODES:sew>.vv\t%0,%1,%2"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vrgatherei<VMODES:mode><VIMODES:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
			(match_operand:<VMODES:VCMPEQUIV> 1 "register_operand")
			(unspec:VMODES
			  [(match_operand:VMODES 3 "register_operand")
			   (match_operand:VIMODES 4 "register_operand")]
			 UNSPEC_VRGATHER)
			(match_operand:VMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vrgatherei<VMODES:mode><VIMODES:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VMODES:VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VMODES
	       [(match_operand:VMODES 3 "register_operand" "vr")
		(match_operand:VIMODES 4 "register_operand" "vr")]
	      UNSPEC_VRGATHER)
	     (match_operand:VMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vrgatherei<VIMODES:sew>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VMODES:VSUBMODE>")])

(define_expand "vrgather<VMODES:mode><P:mode>_scalar"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:VMODES 1 "register_operand")
			 (match_operand:P 2 "reg_or_uimm5_operand")]
		       UNSPEC_VRGATHER)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vrgather<VMODES:mode><P:mode>_scalar_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:VMODES 1 "register_operand" "vr,vr")
	      (match_operand:P 2 "reg_or_uimm5_operand" "r,K")]
	    UNSPEC_VRGATHER)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vrgather.vx\t%0,%1,%2
   vrgather.vi\t%0,%1,%2"
  [(set_attr "type" "vmove,vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vrgather<VMODES:mode><P:mode>_scalar_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VMODES
			  [(match_operand:VMODES 3 "register_operand")
			   (match_operand:P 4 "reg_or_uimm5_operand")]
			 UNSPEC_VRGATHER)
			(match_operand:VMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vrgather<VMODES:mode><P:mode>_scalar_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (unspec:VMODES
	       [(match_operand:VMODES 3 "register_operand" "vr,vr")
		(match_operand:P 4 "reg_or_uimm5_operand" "r,K")]
	      UNSPEC_VRGATHER)
	     (match_operand:VMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   vrgather.vx\t%0,%3,%4,%1.t
   vrgather.vi\t%0,%3,%4,%1.t"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Compress Instruction.

(define_expand "vcompress<mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VMODES 2 "register_operand")
			 (match_operand:VMODES 3 "register_operand")]
		       UNSPEC_VCOMPRESS)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vcompress<mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=&vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	      (match_operand:VMODES 2 "register_operand" "0")
	      (match_operand:VMODES 3 "register_operand" "vr")]
	    UNSPEC_VCOMPRESS)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "vcompress.vm\t%0,%3,%1"
  [(set_attr "type" "vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Narrowing Fixed-Point Clip Instructions

(define_expand "vnclip<v_su><mode>3_nv"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(unspec:VWIMODES
			[(match_operand:<VWMODE> 1 "register_operand")
			 (match_operand:VWIMODES 2 "vector_shift_operand")]
		       UNSPEC_VCLIP)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vnclip<v_su><mode>3_nv_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VWIMODES
	  [(unspec:VWIMODES
	     [(match_operand:<VWMODE> 1 "register_operand" "vr,vr")
	      (match_operand:VWIMODES 2 "vector_shift_operand" "vr,vk")]
	    UNSPEC_VCLIP)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  {
    switch (which_alternative)
      {
      case 0:
	return target_subset_version_p ("v", 0, 7) ? "vnclip<v_su>.vv\t%0,%1,%2" : "vnclip<v_su>.wv\t%0,%1,%2";
      case 1:
	return target_subset_version_p ("v", 0, 7) ? "vnclip<v_su>.vi\t%0,%1,%v2" : "vnclip<v_su>.wi\t%0,%1,%v2";
      default:
	gcc_unreachable ();
      }
  }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vnclip<v_su><mode>3_nv_scalar"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(unspec:VWIMODES
			[(match_operand:<VWMODE> 1 "register_operand")
			 (vec_duplicate:VWIMODES
			   (match_operand:<VSUBMODE> 2 "register_operand"))]
		       UNSPEC_VCLIP)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vnclip<v_su><mode>3_nv_scalar_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr")
	(unspec:VWIMODES
	  [(unspec:VWIMODES
	     [(match_operand:<VWMODE> 1 "register_operand" "vr")
	      (vec_duplicate:VWIMODES
		(match_operand:<VSUBMODE> 2 "register_operand" "r"))]
	    UNSPEC_VCLIP)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vnclip<v_su>.vx\t%0,%1,%2" : "vnclip<v_su>.wx\t%0,%1,%2"; }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vnclip<v_su><mode>3_nv_mask"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(if_then_else:VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VWIMODES
			  [(match_operand:<VWMODE> 3 "register_operand")
			   (match_operand:VWIMODES 4 "vector_shift_operand")]
			 UNSPEC_VCLIP)
			(match_operand:VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vnclip<v_su><mode>3_nv_mask_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr,&vr")
	(unspec:VWIMODES
	  [(if_then_else:VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (unspec:VWIMODES
	       [(match_operand:<VWMODE> 3 "register_operand" "vr,vr")
		(match_operand:VWIMODES 4 "vector_shift_operand" "vr,vk")]
	      UNSPEC_VCLIP)
	     (match_operand:VWIMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  {
    switch (which_alternative)
      {
      case 0:
	return target_subset_version_p ("v", 0, 7) ? "vnclip<v_su>.vv\t%0,%3,%4,%1.t" : "vnclip<v_su>.wv\t%0,%3,%4,%1.t";
      case 1:
	return target_subset_version_p ("v", 0, 7) ? "vnclip<v_su>.vi\t%0,%3,%v4,%1.t" : "vnclip<v_su>.wi\t%0,%3,%v4,%1.t";
      default:
	gcc_unreachable ();
      }
  }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vnclip<v_su><mode>3_nv_scalar_mask"
  [(parallel [(set (match_operand:VWIMODES 0 "register_operand")
		   (unspec:VWIMODES
		     [(if_then_else:VWIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VWIMODES
			  [(match_operand:<VWMODE> 3 "register_operand")
			   (vec_duplicate:VWIMODES
			     (match_operand:<VSUBMODE> 4 "register_operand"))]
			 UNSPEC_VCLIP)
			(match_operand:VWIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vnclip<v_su><mode>3_scalar_nv_mask_nosetvl"
  [(set (match_operand:VWIMODES 0 "register_operand" "=&vr")
	(unspec:VWIMODES
	  [(if_then_else:VWIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VWIMODES
	       [(match_operand:<VWMODE> 3 "register_operand" "vr")
		(vec_duplicate:VWIMODES
		  (match_operand:<VSUBMODE> 4 "register_operand" "r"))]
	      UNSPEC_VCLIP)
	     (match_operand:VWIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vnclip<v_su>.vx\t%0,%3,%4,%1.t" : "vnclip<v_su>.wx\t%0,%3,%4,%1.t"; }
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;;Vector Single-Width Scaling Shift Instructions

(define_expand "<sshift><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (match_operand:VIMODES 2 "vector_shift_operand")]
		       UNSPEC_VSSHIFT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sshift><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:VIMODES 1 "register_operand" "vr,vr")
	      (match_operand:VIMODES 2 "vector_shift_operand" "vr,vk")]
	    UNSPEC_VSSHIFT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   <sshift>.vv\t%0,%1,%2
   <sshift>.vi\t%0,%1,%v2"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sshift><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (vec_duplicate:VIMODES
			   (match_operand:<VSUBMODE> 2 "register_operand"))]
		       UNSPEC_VSSHIFT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sshift><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:VIMODES 1 "register_operand" "vr")
	      (vec_duplicate:VIMODES
		(match_operand:<VSUBMODE> 2 "register_operand" "r"))]
	    UNSPEC_VSSHIFT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<sshift>.vx\t%0,%1,%2"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sshift><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (match_operand:VIMODES 4 "vector_shift_operand")]
			 UNSPEC_VSSHIFT)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sshift><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr,vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm,vm")
	     (unspec:VIMODES
	       [(match_operand:VIMODES 3 "register_operand" "vr,vr")
		(match_operand:VIMODES 4 "vector_shift_operand" "vr,vk")]
	      UNSPEC_VSSHIFT)
	     (match_operand:VIMODES 2 "register_operand" "0,0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "@
   <sshift>.vv\t%0,%3,%4,%1.t
   <sshift>.vi\t%0,%3,%v4,%1.t"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sshift><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (vec_duplicate:VIMODES
			     (match_operand:<VSUBMODE> 4 "register_operand"))]
			 UNSPEC_VSSHIFT)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sshift><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VIMODES
	       [(match_operand:VIMODES 3 "register_operand" "vr")
		(vec_duplicate:VIMODES
		  (match_operand:<VSUBMODE> 4 "register_operand" "r"))]
	      UNSPEC_VSSHIFT)
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<sshift>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "vshift")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])


;;Vector Averaging Add and Subtract
;;and Fractional Multiply with Rounding and Saturation

(define_expand "<sat_op><mode>3"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")]
		       UNSPEC_SAT_OP)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sat_op><mode>3_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:VIMODES 1 "register_operand" "vr")
	      (match_operand:VIMODES 2 "register_operand" "vr")]
	    UNSPEC_SAT_OP)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<sat_op>.vv\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sat_op><mode>3_scalar"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(unspec:VIMODES
			[(match_operand:VIMODES 1 "register_operand")
			 (vec_duplicate:VIMODES
			   (match_operand:<VSUBMODE> 2 "register_operand"))]
		       UNSPEC_SAT_OP)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sat_op><mode>3_scalar_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(unspec:VIMODES
	     [(match_operand:VIMODES 1 "register_operand" "vr")
	      (vec_duplicate:VIMODES
		(match_operand:<VSUBMODE> 2 "register_operand" "r"))]
	    UNSPEC_SAT_OP)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<sat_op>.vx\t%0,%1,%2"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sat_op><mode>3_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (match_operand:VIMODES 4 "register_operand")]
			 UNSPEC_SAT_OP)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sat_op><mode>3_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VIMODES
	       [(match_operand:VIMODES 3 "register_operand" "vr")
		(match_operand:VIMODES 4 "register_operand" "vr")]
	      UNSPEC_SAT_OP)
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<sat_op>.vv\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "<sat_op><mode>3_scalar_mask"
  [(parallel [(set (match_operand:VIMODES 0 "register_operand")
		   (unspec:VIMODES
		     [(if_then_else:VIMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VIMODES
			  [(match_operand:VIMODES 3 "register_operand")
			   (vec_duplicate:VIMODES
			     (match_operand:<VSUBMODE> 4 "register_operand"))]
			 UNSPEC_SAT_OP)
			(match_operand:VIMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*<sat_op><mode>3_scalar_mask_nosetvl"
  [(set (match_operand:VIMODES 0 "register_operand" "=vr")
	(unspec:VIMODES
	  [(if_then_else:VIMODES
	     (match_operand:<VCMPEQUIV> 1 "register_operand" "vm")
	     (unspec:VIMODES
	       [(match_operand:VIMODES 3 "register_operand" "vr")
		(vec_duplicate:VIMODES
		  (match_operand:<VSUBMODE> 4 "register_operand" "r"))]
	      UNSPEC_SAT_OP)
	     (match_operand:VIMODES 2 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  "<sat_op>.vx\t%0,%3,%4,%1.t"
  [(set_attr "type" "varith")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Unit-stride Fault-Only-First Load Instructions

(define_expand "vleff<VMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(unspec:VMODES
			[(mem:VMODES (match_operand:P 1 "register_operand"))]
		       UNSPEC_VLEFF)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vleff<mode>_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=vr")
	(unspec:VMODES
	  [(unspec:VMODES
	     [(match_operand:VMODES 1 "memory_operand"  "m")]
	    UNSPEC_VLEFF)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vleff.v\t%0,%1" : "vle<sew>ff.v\t%0,%1"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vleff<VMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VMODES 0 "register_operand")
		   (unspec:VMODES
		     [(if_then_else:VMODES
			(match_operand:<VCMPEQUIV> 1 "register_operand")
			(unspec:VMODES
			  [(mem:VMODES (match_operand:P 3 "register_operand"))]
			 UNSPEC_VLEFF)
			(match_operand:VMODES 2 "register_operand"))
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vleff<VMODES:mode>_mask_nosetvl"
  [(set (match_operand:VMODES 0 "register_operand" "=vr")
	(unspec:VMODES
	  [(if_then_else:VMODES
	     (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")
	     (unspec:VMODES
	       [(match_operand:VMODES 1 "memory_operand" "m")]
	      UNSPEC_VLEFF)
	     (match_operand:VMODES 3 "register_operand" "0"))
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vleff.v\t%0,%1,%2.t" : "vle<sew>ff.v\t%0,%1,%2.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

;; Read VL
(define_insn "riscv_vreadvl<mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec_volatile:P [(reg:P VL_REGNUM)] UNSPEC_READ_VL))]
  "TARGET_VECTOR"
  "csrr\t%0, vl"
  [(set_attr "type" "vector")
   (set_attr "mode" "none")])

;; Read Vtype
(define_insn "read_vtype<mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P [(reg:P VTYPE_REGNUM)] UNSPEC_READ_VTYPE))]
  "TARGET_VECTOR"
  "csrr\t%0, vtype"
  [(set_attr "type" "vector")
   (set_attr "mode" "none")])

;; Write Vtype
(define_insn "write_vtype<mode>"
  [(set (reg:P VTYPE_REGNUM)
	(unspec:P [(match_operand:P 0 "register_operand" "r")]
	 UNSPEC_WRITE_VTYPE))]
  "TARGET_VECTOR"
  "vsetvl\tx0, x0, %0"
  [(set_attr "type" "vset")
   (set_attr "mode" "none")])

;; Vector segment load/store

(define_expand "vseg_load<VTMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 1 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_load<VTMODES:mode>_<P:mode>"
  [(set (match_operand:VTMODES 0 "register_operand" "=vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlseg<NF>e.v\t%0, (%1)" : "vlseg<NF>e<sew>.v\t%0, (%1)"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_load<VTMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 3 "register_operand")
			 (match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VTMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_load<VTMODES:mode>_<P:mode>_mask"
  [(set (match_operand:VTMODES 0 "register_operand" "=vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")
	      (match_operand:VTMODES 3 "register_operand" "0")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlseg<NF>e.v\t%0, (%1), %2.t" : "vlseg<NF>e<sew>.v\t%0, (%1), %2.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_ff_load<VTMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 1 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_LOAD_FIRST_FAULT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_ff_load<VTMODES:mode>_<P:mode>"
  [(set (match_operand:VTMODES 0 "register_operand" "=vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_LOAD_FIRST_FAULT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlseg<NF>eff.v\t%0, (%1)" : "vlseg<NF>e<sew>ff.v\t%0, (%1)"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_ff_load<VTMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 3 "register_operand")
			 (match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VTMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_LOAD_FIRST_FAULT)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_ff_load<VTMODES:mode>_<P:mode>_mask"
  [(set (match_operand:VTMODES 0 "register_operand" "=vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")
	      (match_operand:VTMODES 3 "register_operand" "0")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_LOAD_FIRST_FAULT)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlseg<NF>eff.v\t%0, (%1), %2.t" : "vlseg<NF>e<sew>ff.v\t%0, (%1), %2.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_idx_<order>load<VTMODES:mode><VIMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_IDX_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_idx_load<order><VTMODES:mode><VIMODES:mode>_<P:mode>"
  [(set (match_operand:VTMODES 0 "register_operand" "=&vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:VIMODES 2 "register_operand" "vr")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_IDX_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlxseg<NF>e.v\t%0, (%1), %2" : "vl<order>xseg<NF>ei<VIMODES:sew>.v\t%0, (%1), %2"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_idx_<order>load<VTMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 3 "register_operand")
			 (match_operand:VIMODES 4 "register_operand")
			 (match_operand:<VTMODES:VCMPEQUIV> 1 "register_operand")
			 (match_operand:VTMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_IDX_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_idx_<order>load<VTMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(set (match_operand:VTMODES 0 "register_operand" "=&vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:VIMODES 4 "register_operand" "vr")
	      (match_operand:<VTMODES:VCMPEQUIV> 2 "register_operand" "vm")
	      (match_operand:VTMODES 3 "register_operand" "0")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_IDX_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlxseg<NF>e.v\t%0, (%1), %4, %2.t" : "vl<order>xseg<NF>ei<VIMODES:sew>.v\t%0, (%1), %4, %2.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_strided_load<VTMODES:mode>_<P:mode>"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 1 "register_operand")
			 (match_operand:P 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_strided_load<VTMODES:mode>_<P:mode>"
  [(set (match_operand:VTMODES 0 "register_operand" "=vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:P 2 "register_operand" "r")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlsseg<NF>e.v\t%0, (%1), %2" : "vlsseg<NF>e<sew>.v\t%0, (%1), %2"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_strided_load<VTMODES:mode>_<P:mode>_mask"
  [(parallel [(set (match_operand:VTMODES 0 "register_operand")
		   (unspec:VTMODES
		     [(unspec:VTMODES
			[(match_operand:P 3 "register_operand")
			 (match_operand:P 4 "register_operand")
			 (match_operand:<VCMPEQUIV> 1 "register_operand")
			 (match_operand:VTMODES 2 "register_operand")
			 (mem:BLK (scratch))]
		       UNSPEC_SEG_LOAD)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_strided_load<VTMODES:mode>_<P:mode>_mask"
  [(set (match_operand:VTMODES 0 "register_operand" "=vr")
	(unspec:VTMODES
	  [(unspec:VTMODES
	     [(match_operand:P 1 "register_operand" "r")
	      (match_operand:P 4 "register_operand" "r")
	      (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")
	      (match_operand:VTMODES 3 "register_operand" "0")
	      (mem:BLK (scratch))]
	    UNSPEC_SEG_LOAD)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vlsseg<NF>e.v\t%0, (%1), %4, %2.t" : "vlsseg<NF>e<sew>.v\t%0, (%1), %4, %2.t"; }
  [(set_attr "type" "vload")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_store<VTMODES:mode>_<P:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 1 "register_operand")
			 (match_operand:VTMODES 0 "register_operand")]
		       UNSPEC_SEG_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_store<VTMODES:mode>_<P:mode>"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:VTMODES 1 "register_operand" "vr")]
	    UNSPEC_SEG_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsseg<NF>e.v\t%1, (%0)" : "vsseg<NF>e<sew>.v\t%1, (%0)"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_store<VTMODES:mode>_<P:mode>_mask"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 2 "register_operand")
			 (match_operand:VTMODES 1 "register_operand")
			 (match_operand:<VCMPEQUIV> 0 "register_operand")]
		       UNSPEC_SEG_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_store<VTMODES:mode>_<P:mode>_mask"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:VTMODES 1 "register_operand" "vr")
	      (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")]
	    UNSPEC_SEG_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsseg<NF>e.v\t%1, (%0), %2.t" : "vsseg<NF>e<sew>.v\t%1, (%0), %2.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_idx_<order>store<VTMODES:mode><VIMODES:mode>_<P:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 1 "register_operand")
			 (match_operand:VIMODES 2 "register_operand")
			 (match_operand:VTMODES 0 "register_operand")]
		       UNSPEC_SEG_IDX_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_idx_<order>store<VTMODES:mode><VIMODES:mode>_<P:mode>"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:VIMODES 2 "register_operand" "vr")
	      (match_operand:VTMODES 1 "register_operand" "vr")]
	    UNSPEC_SEG_IDX_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsxseg<NF>e.v\t%1, (%0), %2" : "vs<order>xseg<NF>ei<VIMODES:sew>.v\t%1, (%0), %2"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_idx_<order>store<VTMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 2 "register_operand")
			 (match_operand:VIMODES 3 "register_operand")
			 (match_operand:VTMODES 1 "register_operand")
			 (match_operand:<VTMODES:VCMPEQUIV> 0 "register_operand")]
		       UNSPEC_SEG_IDX_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_idx_<order>store<VTMODES:mode><VIMODES:mode>_<P:mode>_mask"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:VIMODES 3 "register_operand" "vr")
	      (match_operand:VTMODES 1 "register_operand" "vr")
	      (match_operand:<VTMODES:VCMPEQUIV> 2 "register_operand" "vm")]
	    UNSPEC_SEG_IDX_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VTMODES:VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vsxseg<NF>e.v\t%1, (%0), %3, %2.t" : "vs<order>xseg<NF>ei<VIMODES:sew>.v\t%1, (%0), %3, %2.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VTMODES:VSUBMODE>")])

(define_expand "vseg_strided_store<VTMODES:mode>_<P:mode>"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 1 "register_operand")
			 (match_operand:P 2 "register_operand")
			 (match_operand:VTMODES 0 "register_operand")]
		       UNSPEC_SEG_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_strided_store<VTMODES:mode>_<P:mode>"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:P 2 "register_operand" "r")
	      (match_operand:VTMODES 1 "register_operand" "vr")]
	    UNSPEC_SEG_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vssseg<NF>e.v\t%1, (%0), %2" : "vssseg<NF>e<sew>.v\t%1, (%0), %2"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vseg_strided_store<VTMODES:mode>_<P:mode>_mask"
  [(parallel [(set (mem:BLK (scratch))
		   (unspec:BLK
		     [(unspec:BLK
			[(match_operand:P 2 "register_operand")
			 (match_operand:P 3 "register_operand")
			 (match_operand:VTMODES 1 "register_operand")
			 (match_operand:<VCMPEQUIV> 0 "register_operand")]
		       UNSPEC_SEG_STORE)
		      (reg:SI VL_REGNUM)]
		    UNSPEC_USEVL))
	      (use (reg:<VLMODE> VTYPE_REGNUM))])]
  "TARGET_VECTOR"
{
})

(define_insn "*vseg_strided_store<VTMODES:mode>_<P:mode>_mask"
  [(set (mem:BLK (scratch))
	(unspec:BLK
	  [(unspec:BLK
	     [(match_operand:P 0 "register_operand" "r")
	      (match_operand:P 3 "register_operand" "r")
	      (match_operand:VTMODES 1 "register_operand" "vr")
	      (match_operand:<VCMPEQUIV> 2 "register_operand" "vm")]
	    UNSPEC_SEG_STORE)
	   (reg:SI VL_REGNUM)]
	 UNSPEC_USEVL))
   (use (reg:<VLMODE> VTYPE_REGNUM))]
  "TARGET_VECTOR"
  { return target_subset_version_p ("v", 0, 7) ? "vssseg<NF>e.v\t%1, (%0), %3, %2.t" : "vssseg<NF>e<sew>.v\t%1, (%0), %3, %2.t"; }
  [(set_attr "type" "vstore")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])


(define_insn_and_split "mov<mode>"
  [(set (match_operand:VTMODES 0 "nonimmediate_operand" "=vr,vr, m,vr")
		(match_operand:VTMODES 1 "vector_move_operand"  " vr, m,vr,vc"))
   (clobber (match_scratch:SI 2 "=X,&r,&r,&r"))
   (clobber (match_scratch:SI 3 "=X,&r,&r,X"))]
  "TARGET_VECTOR"
  "#"
  "&& reload_completed"
  [(const_int 0)]
{
  int i;
  if (REG_P (operands[0]) && REG_P (operands[1]))
    {
      for (i = 0; i < riscv_get_nf (<MODE>mode); ++i)
	{
	  poly_int64 offset = i * GET_MODE_SIZE (<VTSUBMODE>mode);
	  rtx dst_subreg = simplify_gen_subreg (<VTSUBMODE>mode, operands[0],
						<MODE>mode, offset);
	  rtx src_subreg = simplify_gen_subreg (<VTSUBMODE>mode, operands[1],
						<MODE>mode, offset);
	  emit_move_insn (dst_subreg, src_subreg);
	}
    }
  else if (REG_P (operands[0]) && MEM_P (operands[1]))
    {
      PUT_MODE (operands[2], Pmode);
      PUT_MODE (operands[3], Pmode);
      rtx lmul = GEN_INT (exact_log2 (riscv_get_lmul (<MODE>mode)));
      emit_insn (gen_read_vlenb (operands[2]));
      if (TARGET_64BIT)
	emit_insn (gen_ashldi3 (operands[2], operands[2], lmul));
      else
	emit_insn (gen_ashlsi3 (operands[2], operands[2], lmul));
      emit_move_insn (operands[3], XEXP (operands[1], 0));

      for (i = 0; i < riscv_get_nf (<MODE>mode); ++i)
	{
	  poly_int64 offset = i * GET_MODE_SIZE (<VTSUBMODE>mode);
	  rtx dst = simplify_gen_subreg (<VTSUBMODE>mode, operands[0],
						<MODE>mode, offset);
	  if (i != 0)
	    emit_insn (gen_add3_insn (operands[3], operands[3], operands[2]));
	  rtx src = gen_rtx_MEM (<VTSUBMODE>mode, operands[3]);
	  emit_move_insn (dst, src);
	}
    }
  else if (REG_P (operands[0]) && GET_CODE (operands[1]) == CONST_VECTOR)
    {
      rtx val;
      if (!const_vec_duplicate_p (operands[1], &val))
	gcc_unreachable ();

      PUT_MODE (operands[2], <VSUBMODE>mode);
      emit_move_insn (operands[2], val);

      for (i = 0; i < riscv_get_nf (<MODE>mode); ++i)
	{
	  poly_int64 offset = i * GET_MODE_SIZE (<VTSUBMODE>mode);
	  rtx dst_subreg = simplify_gen_subreg (<VTSUBMODE>mode, operands[0],
						<MODE>mode, offset);

          emit_insn (gen_vec_duplicate<vtsubmode> (dst_subreg, operands[2]));
	}
    }
  else if (MEM_P (operands[0]) && REG_P (operands[1]))
    {
      PUT_MODE (operands[2], Pmode);
      PUT_MODE (operands[3], Pmode);
      rtx lmul = GEN_INT (exact_log2 (riscv_get_lmul (<MODE>mode)));
      emit_insn (gen_read_vlenb (operands[2]));
      if (TARGET_64BIT)
	emit_insn (gen_ashldi3 (operands[2], operands[2], lmul));
      else
	emit_insn (gen_ashlsi3 (operands[2], operands[2], lmul));
      emit_move_insn (operands[3], XEXP (operands[0], 0));

      for (i = 0; i < riscv_get_nf (<MODE>mode); ++i)
	{
	  poly_int64 offset = i * GET_MODE_SIZE (<VTSUBMODE>mode);
	  rtx src = simplify_gen_subreg (<VTSUBMODE>mode, operands[1],
						<MODE>mode, offset);
	  if (i != 0)
	    emit_insn (gen_add3_insn (operands[3], operands[3], operands[2]));
	  rtx dst = gen_rtx_MEM (<VTSUBMODE>mode, operands[3]);
	  emit_move_insn (dst, src);
	}
    }
  else
    gcc_unreachable ();
  DONE;
}
  [(set_attr "type" "vmove,vload,vstore,vmove")
   (set_attr "mode" "none")
   (set_attr "emode" "<VSUBMODE>")])

(define_expand "vtuple_insert<mode>"
  [(match_operand:VTMODES 0 "register_operand")
   (match_operand:VTMODES 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:SI 3 "const_int_operand" "")]
  "TARGET_VECTOR"
{
  if (INTVAL (operands[3]) < 0
      || (INTVAL (operands[3]) >= riscv_get_nf (<MODE>mode)))
    {
      gcc_unreachable ();
      FAIL;
    }
  poly_int64 offset = INTVAL (operands[3]) * GET_MODE_SIZE (<VTSUBMODE>mode);
  emit_move_insn (operands[0], operands[1]);
  rtx subreg = simplify_gen_subreg (<VTSUBMODE>mode, operands[0],
				    <MODE>mode, offset);
  emit_move_insn (subreg, operands[2]);
  DONE;
})

(define_expand "vtuple_extract<mode>"
  [(match_operand:<VTSUBMODE> 0 "register_operand")
   (match_operand:VTMODES 1 "register_operand")
   (match_operand:SI 2 "const_int_operand")]
  "TARGET_VECTOR"
{
  if (INTVAL (operands[2]) < 0
      || (INTVAL (operands[2]) >= riscv_get_nf (<MODE>mode)))
    {
      gcc_unreachable ();
      FAIL;
    }
  poly_int64 offset = INTVAL (operands[2]) * GET_MODE_SIZE (<VTSUBMODE>mode);
  rtx subreg = simplify_gen_subreg (<VTSUBMODE>mode, operands[1],
				    <MODE>mode, offset);
  emit_move_insn (operands[0], subreg);
  DONE;
})

;; Maybe use define_insn_and_split might get better optimization?
(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF2MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF3MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:<VTSUBMODE> 3 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF4MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:<VTSUBMODE> 3 "register_operand")
   (match_operand:<VTSUBMODE> 4 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF5MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:<VTSUBMODE> 3 "register_operand")
   (match_operand:<VTSUBMODE> 4 "register_operand")
   (match_operand:<VTSUBMODE> 5 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF6MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:<VTSUBMODE> 3 "register_operand")
   (match_operand:<VTSUBMODE> 4 "register_operand")
   (match_operand:<VTSUBMODE> 5 "register_operand")
   (match_operand:<VTSUBMODE> 6 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF7MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:<VTSUBMODE> 3 "register_operand")
   (match_operand:<VTSUBMODE> 4 "register_operand")
   (match_operand:<VTSUBMODE> 5 "register_operand")
   (match_operand:<VTSUBMODE> 6 "register_operand")
   (match_operand:<VTSUBMODE> 7 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vtuple_create<mode>"
  [(match_operand:VTNF8MODES 0 "register_operand")
   (match_operand:<VTSUBMODE> 1 "register_operand")
   (match_operand:<VTSUBMODE> 2 "register_operand")
   (match_operand:<VTSUBMODE> 3 "register_operand")
   (match_operand:<VTSUBMODE> 4 "register_operand")
   (match_operand:<VTSUBMODE> 5 "register_operand")
   (match_operand:<VTSUBMODE> 6 "register_operand")
   (match_operand:<VTSUBMODE> 7 "register_operand")
   (match_operand:<VTSUBMODE> 8 "register_operand")]
  "TARGET_VECTOR"
{
  riscv_expand_vtuple_create (operands);
  DONE;
})

(define_expand "vundefined_<mode>"
  [(clobber (match_operand:VMODES 0 "register_operand"))]
  "TARGET_VECTOR"
{
})

(define_expand "vundefined_<mode>"
  [(clobber (match_operand:VTMODES 0 "register_operand"))]
  "TARGET_VECTOR"
{
})

(define_expand "vector_insert<mode>"
  [(match_operand:VMODES 0 "register_operand")
   (match_operand:VMODES 1 "register_operand")
   (match_operand 2 "register_operand")
   (match_operand:SI 3 "const_int_operand" "")]
  "TARGET_VECTOR"
{
  if (INTVAL (operands[3]) < 0)
    {
      gcc_unreachable ();
      FAIL;
    }
  poly_int64 offset = INTVAL (operands[3]) * GET_MODE_SIZE (GET_MODE (operands[2]));
  emit_move_insn (operands[0], operands[1]);
  rtx subreg = simplify_gen_subreg (GET_MODE (operands[2]), operands[0],
				    <MODE>mode, offset);
  emit_move_insn (subreg, operands[2]);
  DONE;
})

(define_expand "vector_extract<mode>"
  [(match_operand 0 "register_operand")
   (match_operand:VMODES 1 "register_operand")
   (match_operand:SI 2 "const_int_operand")]
  "TARGET_VECTOR"
{
  if (INTVAL (operands[2]) < 0)
    {
      gcc_unreachable ();
      FAIL;
    }
  poly_int64 offset = INTVAL (operands[2]) * GET_MODE_SIZE (GET_MODE (operands[0]));
  rtx subreg = simplify_gen_subreg (GET_MODE (operands[0]), operands[1],
				    <MODE>mode, offset);
  if(!subreg)
    {
      gcc_unreachable ();
      FAIL;
    }
  emit_move_insn (operands[0], subreg);
  DONE;
})

(include "vector-v0p7.md")
(include "vector-thead.md")

;; 移植
(define_constants [
   (INVALID_ATTRIBUTE            255)
   (X0_REGNUM                      0)
])

;; The default SEW of RVV instruction. This attribute doesn't mean the instruction
;; is necessary to require SEW check for example vlm.v which require ratio to
;; check. However, we need default value of SEW for vsetvl instruction since there
;; is no field for ratio in the vsetvl instruction encoding.
(define_attr "sew" ""
  (cond [(eq_attr "mode" "VNx2QI,VNx4QI,VNx8QI,VNx16QI,VNx32QI,VNx64QI,\
			  VNx2BI,VNx4BI,VNx8BI,VNx16BI,VNx32BI,VNx64BI,\
			  VNx128QI,VNx128BI,VNx2x64QI,VNx2x32QI,VNx3x32QI,VNx4x32QI,\
			  VNx2x16QI,VNx3x16QI,VNx4x16QI,VNx5x16QI,VNx6x16QI,VNx7x16QI,VNx8x16QI,\
			  VNx2x8QI,VNx3x8QI,VNx4x8QI,VNx5x8QI,VNx6x8QI,VNx7x8QI,VNx8x8QI,\
			  VNx2x4QI,VNx3x4QI,VNx4x4QI,VNx5x4QI,VNx6x4QI,VNx7x4QI,VNx8x4QI,\
			  VNx2x2QI,VNx3x2QI,VNx4x2QI,VNx5x2QI,VNx6x2QI,VNx7x2QI,VNx8x2QI")
	 (const_int 8)
	 (eq_attr "mode" "VNx2HI,VNx4HI,VNx8HI,VNx16HI,VNx32HI,VNx64HI,\
			  VNx2x32HI,VNx2x16HI,VNx3x16HI,VNx4x16HI,\
			  VNx2x8HI,VNx3x8HI,VNx4x8HI,VNx5x8HI,VNx6x8HI,VNx7x8HI,VNx8x8HI,\
			  VNx2x4HI,VNx3x4HI,VNx4x4HI,VNx5x4HI,VNx6x4HI,VNx7x4HI,VNx8x4HI,\
			  VNx2x2HI,VNx3x2HI,VNx4x2HI,VNx5x2HI,VNx6x2HI,VNx7x2HI,VNx8x2HI")
	 (const_int 16)
	 (eq_attr "mode" "VNx2SI,VNx4SI,VNx8SI,VNx16SI,VNx32SI,\
			  VNx2SF,VNx4SF,VNx8SF,VNx16SF,VNx32SF,\
			  VNx2x16SI,VNx2x8SI,VNx3x8SI,VNx4x8SI,\
			  VNx2x4SI,VNx3x4SI,VNx4x4SI,VNx5x4SI,VNx6x4SI,VNx7x4SI,VNx8x4SI,\
			  VNx2x2SI,VNx3x2SI,VNx4x2SI,VNx5x2SI,VNx6x2SI,VNx7x2SI,VNx8x2SI,\
			  VNx2x16SF,VNx2x8SF,VNx3x8SF,VNx4x8SF,\
			  VNx2x4SF,VNx3x4SF,VNx4x4SF,VNx5x4SF,VNx6x4SF,VNx7x4SF,VNx8x4SF,\
			  VNx2x2SF,VNx3x2SF,VNx4x2SF,VNx5x2SF,VNx6x2SF,VNx7x2SF,VNx8x2SF")
	 (const_int 32)
	 (eq_attr "mode" "VNx2DI,VNx4DI,VNx8DI,VNx16DI,\
			  VNx2DF,VNx4DF,VNx8DF,VNx16DF,\
			  VNx2x8DI,VNx2x4DI,VNx3x4DI,VNx4x4DI,\
			  VNx2x2DI,VNx3x2DI,VNx4x2DI,VNx5x2DI,VNx6x2DI,VNx7x2DI,VNx8x2DI,\
			  VNx2x8DF,VNx2x4DF,VNx3x4DF,VNx4x4DF,\
			  VNx2x2DF,VNx3x2DF,VNx4x2DF,VNx5x2DF,VNx6x2DF,VNx7x2DF,VNx8x2DF")
	 (const_int 64)]
	(const_int INVALID_ATTRIBUTE)))

;; Ditto to LMUL.
(define_attr "vlmul" ""
  (cond [
	 (eq_attr "mode" "VNx2QI,VNx2BI,VNx2x2QI,VNx3x2QI,VNx4x2QI,VNx5x2QI,VNx6x2QI,VNx7x2QI,VNx8x2QI")
	   (symbol_ref "get_vlmul(E_VNx2QImode)")
	 (eq_attr "mode" "VNx4QI,VNx4BI,VNx2x4QI,VNx3x4QI,VNx4x4QI,VNx5x4QI,VNx6x4QI,VNx7x4QI,VNx8x4QI")
	   (symbol_ref "get_vlmul(E_VNx4QImode)")
	 (eq_attr "mode" "VNx8QI,VNx8BI,VNx2x8QI,VNx3x8QI,VNx4x8QI,VNx5x8QI,VNx6x8QI,VNx7x8QI,VNx8x8QI")
	   (symbol_ref "get_vlmul(E_VNx8QImode)")
	 (eq_attr "mode" "VNx16QI,VNx16BI,VNx2x16QI,VNx3x16QI,VNx4x16QI,VNx5x16QI,VNx6x16QI,VNx7x16QI,VNx8x16QI")
	   (symbol_ref "get_vlmul(E_VNx16QImode)")
	 (eq_attr "mode" "VNx32QI,VNx32BI,VNx2x32QI,VNx3x32QI,VNx4x32QI")
	   (symbol_ref "get_vlmul(E_VNx32QImode)")
	 (eq_attr "mode" "VNx64QI,VNx64BI,VNx2x64QI")
	   (symbol_ref "get_vlmul(E_VNx64QImode)")
	 (eq_attr "mode" "VNx128QI,VNx128BI")
	   (symbol_ref "get_vlmul(E_VNx128QImode)")
	 (eq_attr "mode" "VNx2HI,VNx2x2HI,VNx3x2HI,VNx4x2HI,VNx5x2HI,VNx6x2HI,VNx7x2HI,VNx8x2HI")
	   (symbol_ref "get_vlmul(E_VNx2HImode)")
	 (eq_attr "mode" "VNx4HI,VNx2x4HI,VNx3x4HI,VNx4x4HI,VNx5x4HI,VNx6x4HI,VNx7x4HI,VNx8x4HI")
	   (symbol_ref "get_vlmul(E_VNx4HImode)")
	 (eq_attr "mode" "VNx8HI,VNx2x8HI,VNx3x8HI,VNx4x8HI,VNx5x8HI,VNx6x8HI,VNx7x8HI,VNx8x8HI")
	   (symbol_ref "get_vlmul(E_VNx8HImode)")
	 (eq_attr "mode" "VNx16HI,VNx2x16HI,VNx3x16HI,VNx4x16HI")
	   (symbol_ref "get_vlmul(E_VNx16HImode)")
	 (eq_attr "mode" "VNx32HI,VNx2x32HI")
	   (symbol_ref "get_vlmul(E_VNx32HImode)")
	 (eq_attr "mode" "VNx64HI")
	   (symbol_ref "get_vlmul(E_VNx64HImode)")
	 (eq_attr "mode" "VNx2SI,VNx2SF,VNx2x2SI,VNx3x2SI,VNx4x2SI,VNx5x2SI,VNx6x2SI,VNx7x2SI,VNx8x2SI,\
	 		  VNx2x2SF,VNx3x2SF,VNx4x2SF,VNx5x2SF,VNx6x2SF,VNx7x2SF,VNx8x2SF")
	   (symbol_ref "get_vlmul(E_VNx2SImode)")
	 (eq_attr "mode" "VNx4SI,VNx4SF,VNx2x4SI,VNx3x4SI,VNx4x4SI,VNx5x4SI,VNx6x4SI,VNx7x4SI,VNx8x4SI,\
	 		  VNx2x4SF,VNx3x4SF,VNx4x4SF,VNx5x4SF,VNx6x4SF,VNx7x4SF,VNx8x4SF")
	   (symbol_ref "get_vlmul(E_VNx4SImode)")
	 (eq_attr "mode" "VNx8SI,VNx8SF,VNx2x8SI,VNx3x8SI,VNx4x8SI,VNx2x8SF,VNx3x8SF,VNx4x8SF")
	   (symbol_ref "get_vlmul(E_VNx8SImode)")
	 (eq_attr "mode" "VNx16SI,VNx16SF,VNx2x16SI,VNx2x16SF")
	   (symbol_ref "get_vlmul(E_VNx16SImode)")
	 (eq_attr "mode" "VNx32SI,VNx32SF")
	   (symbol_ref "get_vlmul(E_VNx32SImode)")
	 (eq_attr "mode" "VNx2DI,VNx2DF,VNx2x2DI,VNx3x2DI,VNx4x2DI,VNx5x2DI,VNx6x2DI,VNx7x2DI,VNx8x2DI,\
	 		  VNx2x2DF,VNx3x2DF,VNx4x2DF,VNx5x2DF,VNx6x2DF,VNx7x2DF,VNx8x2DF")
	   (symbol_ref "get_vlmul(E_VNx2DImode)")
	 (eq_attr "mode" "VNx4DI,VNx4DF,VNx2x4DI,VNx3x4DI,VNx4x4DI,VNx2x4DF,VNx3x4DF,VNx4x4DF")
	   (symbol_ref "get_vlmul(E_VNx4DImode)")
	 (eq_attr "mode" "VNx8DI,VNx8DF,VNx2x8DI,VNx2x8DF")
	   (symbol_ref "get_vlmul(E_VNx8DImode)")
	 (eq_attr "mode" "VNx16DI,VNx16DF")
	   (symbol_ref "get_vlmul(E_VNx16DImode)")]
	(const_int INVALID_ATTRIBUTE)))


;; The tail policy op value.
(define_attr "ta" ""
  (cond [
	 (eq_attr "type" "   vset,vsetvl,varith,vcmp,vshift,vlogical,vmove,vmask,vred_bit,vmul,vmadd,vred_sum,\
   vred_max,vdiv,vload,vstore,\
   vfadd,vfsgnj,vfmul,vfwmul,vfmadd,vfwmadd,vfred,vfmove,vfmax,vfcmp,vfcvt,\
   vfdiv,vfsqrt,vfrec,vfclass")
	   (symbol_ref "0")]
	(const_int 0)))

;; The mask policy op value.
(define_attr "ma" ""
  (cond [
	 (eq_attr "type" "   vset,vsetvl,varith,vcmp,vshift,vlogical,vmove,vmask,vred_bit,vmul,vmadd,vred_sum,\
   vred_max,vdiv,vload,vstore,\
   vfadd,vfsgnj,vfmul,vfwmul,vfmadd,vfwmadd,vfred,vfmove,vfmax,vfcmp,vfcvt,\
   vfdiv,vfsqrt,vfrec,vfclass")
	   (symbol_ref "0")]
	(const_int 0)))

(define_insn "@vsetvl<mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P [(match_operand:P 1 "csr_operand" "rK")
		   (match_operand 2 "const_int_operand" "i")
		   (match_operand 3 "const_int_operand" "i")
		   (match_operand 4 "const_int_operand" "i")
		   (match_operand 5 "const_int_operand" "i")] UNSPEC_VSETVL))
   (set (reg:SI VL_REGNUM)
	(unspec:SI [(match_dup 1)
		    (match_dup 2)
		    (match_dup 3)] UNSPEC_VSETVL))
   (set (reg:SI VTYPE_REGNUM)
	(unspec:SI [(match_dup 2)
		    (match_dup 3)
		    (match_dup 4)
		    (match_dup 5)] UNSPEC_VSETVL))]
  "TARGET_VECTOR"
  "vset%i1vli\t%0,%1,e%2,%m3,t%p4,m%p5"
  [(set_attr "type" "vsetvl")
   (set_attr "mode" "<MODE>")
   (set (attr "sew") (symbol_ref "INTVAL (operands[2])"))
   (set (attr "vlmul") (symbol_ref "INTVAL (operands[3])"))
   (set (attr "ta") (symbol_ref "INTVAL (operands[4])"))
   (set (attr "ma") (symbol_ref "INTVAL (operands[5])"))])

(define_insn "@vlmax_avl<mode>"
  [(set (match_operand:P 0 "register_operand" "=r")
	(unspec:P [(match_operand:P 1 "const_int_operand" "i")] UNSPEC_VLMAX))]
  "TARGET_VECTOR"
  "")

(define_insn "@vundefined<mode>"
  [(set (match_operand:V 0 "register_operand" "=vr")
	(unspec:V [(reg:SI X0_REGNUM)] UNSPEC_VUNDEF))]
  "TARGET_VECTOR"
  "")

(define_insn "@vundefined<mode>"
  [(set (match_operand:VB 0 "register_operand" "=vr")
	(unspec:VB [(reg:SI X0_REGNUM)] UNSPEC_VUNDEF))]
  "TARGET_VECTOR"
  "")

(define_expand "@vreinterpret<mode>"
  [(set (match_operand:V 0 "register_operand")
	(match_operand 1 "vector_any_register_operand"))]
  "TARGET_VECTOR"
  {
    emit_move_insn (operands[0], gen_lowpart (<MODE>mode, operands[1]));
    DONE;
  }
)

(define_insn "@pred_series<mode>"
  [(set (match_operand:VI 0 "register_operand"           "=vd, vd, vr, vr")
	(if_then_else:VI
	  (unspec:<VM>
	    [(match_operand:<VM> 1 "vector_mask_operand" " vm, vm,Wc1,Wc1")
	     (match_operand 3 "vector_length_operand"    " rK, rK, rK, rK")
	     (match_operand 4 "const_int_operand"        "  i,  i,  i,  i")
	     (match_operand 5 "const_int_operand"        "  i,  i,  i,  i")
	     (match_operand 6 "const_int_operand"        "  i,  i,  i,  i")
	     (reg:SI VL_REGNUM)
	     (reg:SI VTYPE_REGNUM)] UNSPEC_VPREDICATE)
	  (vec_series:VI (const_int 0) (const_int 1))
	  (match_operand:VI 2 "vector_merge_operand"     " vu,  0, vu,  0")))]
  "TARGET_VECTOR"
  "vid.v\t%0%p1"
  [(set_attr "type" "unknown")     ;; 这里当前版本不支持vmidx，所以先换个别的指令临时替代
   (set_attr "mode" "<MODE>")])


;; -------------------------------------------------------------------------------
;; ---- Predicated fixed-point operations
;; -------------------------------------------------------------------------------
;; Includes:
;; - 12.1 Vector Single-Width Saturating Add and Subtract
;; - 12.2 Vector Single-Width Aaveraging Add and Subtract
;; - 12.3 Vector Single-Width Fractional Multiply with Rounding and Saturation
;; - 12.4 Vector Single-Width Scaling Shift Instructions
;; - 12.5 Vector Narrowing Fixed-Point Clip Instructions
;; -------------------------------------------------------------------------------

;; Saturating Add and Subtract
(define_insn "@pred_<optab><mode>"
  [(set (match_operand:VI 0 "register_operand"           "=vd, vd, vr, vr, vd, vd, vr, vr")
	(if_then_else:VI
	  (unspec:<VM>
	    [(match_operand:<VM> 1 "vector_mask_operand" " vm, vm,Wc1,Wc1, vm, vm,Wc1,Wc1")
	     (match_operand 5 "vector_length_operand"    " rK, rK, rK, rK, rK, rK, rK, rK")
	     (match_operand 6 "const_int_operand"        "  i,  i,  i,  i,  i,  i,  i,  i")
	     (match_operand 7 "const_int_operand"        "  i,  i,  i,  i,  i,  i,  i,  i")
	     (match_operand 8 "const_int_operand"        "  i,  i,  i,  i,  i,  i,  i,  i")
	     (reg:SI VL_REGNUM)
	     (reg:SI VTYPE_REGNUM)] UNSPEC_VPREDICATE)
	  (any_sat_int_binop:VI
	    (match_operand:VI 3 "<binop_rhs1_predicate>" " vr, vr, vr, vr, vr, vr, vr, vr")
	    (match_operand:VI 4 "<binop_rhs2_predicate>" "<binop_rhs2_constraint>"))
	  (match_operand:VI 2 "vector_merge_operand"     " vu,  0, vu,  0, vu,  0, vu,  0")))]
  "TARGET_VECTOR"
  "@
   v<insn>.vv\t%0,%3,%4%p1
   v<insn>.vv\t%0,%3,%4%p1
   v<insn>.vv\t%0,%3,%4%p1
   v<insn>.vv\t%0,%3,%4%p1
   v<binop_vi_variant_insn>\t%0,<binop_vi_variant_op>%p1
   v<binop_vi_variant_insn>\t%0,<binop_vi_variant_op>%p1
   v<binop_vi_variant_insn>\t%0,<binop_vi_variant_op>%p1
   v<binop_vi_variant_insn>\t%0,<binop_vi_variant_op>%p1"
  [(set_attr "type" "<int_binop_insn_type>")
   (set_attr "mode" "<MODE>")])

(define_insn "@pred_<optab><mode>"
  [(set (match_operand:VF 0 "register_operand"           "=vd, vd, vr, vr")
	(if_then_else:VF
	  (unspec:<VM>
	    [(match_operand:<VM> 1 "vector_mask_operand" " vm, vm,Wc1,Wc1")
	     (match_operand 5 "vector_length_operand"    " rK, rK, rK, rK")
	     (match_operand 6 "const_int_operand"        "  i,  i,  i,  i")
	     (match_operand 7 "const_int_operand"        "  i,  i,  i,  i")
	     (match_operand 8 "const_int_operand"        "  i,  i,  i,  i")
	     (reg:SI VL_REGNUM)
	     (reg:SI VTYPE_REGNUM)] UNSPEC_VPREDICATE)
	  (any_float_binop:VF
	    (match_operand:VF 3 "register_operand"       " vr, vr, vr, vr")
	    (match_operand:VF 4 "register_operand"       " vr, vr, vr, vr"))
	  (match_operand:VF 2 "vector_merge_operand"     " vu,  0, vu,  0")))]
  "TARGET_VECTOR"
  "vf<insn>.vv\t%0,%3,%4%p1"
  [(set_attr "type" "<float_insn_type>")
   (set_attr "mode" "<MODE>")])

(define_expand "@pred_<optab><mode>_scalar"
  [(set (match_operand:VI_D 0 "register_operand")
	(if_then_else:VI_D
	  (unspec:<VM>
	    [(match_operand:<VM> 1 "vector_mask_operand")
	     (match_operand 5 "vector_length_operand")
	     (match_operand 6 "const_int_operand")
	     (match_operand 7 "const_int_operand")
	     (match_operand 8 "const_int_operand")
	     (reg:SI VL_REGNUM)
	     (reg:SI VTYPE_REGNUM)] UNSPEC_VPREDICATE)
	  (sat_int_minus_binop:VI_D
	    (match_operand:VI_D 3 "register_operand")
	    (vec_duplicate:VI_D
	      (match_operand:<VEL> 4 "reg_or_int_operand")))
	  (match_operand:VI_D 2 "vector_merge_operand")))]
  "TARGET_VECTOR"
{
  if (sew64_scalar_helper (
	operands,
	/* scalar op */&operands[4],
	/* vl */operands[5],
	<MODE>mode,
	<VM>mode,
	has_vi_variant_p (<CODE>, operands[4]),
	[] (rtx *operands, rtx boardcast_scalar) {
	  emit_insn (gen_pred_<optab><mode> (operands[0], operands[1],
	       operands[2], operands[3], boardcast_scalar, operands[5],
	       operands[6], operands[7], operands[8]));
        }))
    DONE;
})

;; vlm.v/vsm.v/vmclr.m/vmset.m.
;; constraint alternative 0 match vlm.v.
;; constraint alternative 1 match vsm.v.
;; constraint alternative 3 match vmclr.m.
;; constraint alternative 4 match vmset.m.
(define_insn_and_split "@pred_mov<mode>"
  [(set (match_operand:VB 0 "nonimmediate_operand"               "=vr,   m,  vr,  vr,  vr")
	(if_then_else:VB
	  (unspec:VB
	    [(match_operand:VB 1 "vector_all_trues_mask_operand" "Wc1, Wc1, Wc1, Wc1, Wc1")
	     (match_operand 4 "vector_length_operand"            " rK,  rK,  rK,  rK,  rK")
	     (match_operand 5 "const_int_operand"                "  i,   i,   i,   i,   i")
	     (reg:SI VL_REGNUM)
	     (reg:SI VTYPE_REGNUM)] UNSPEC_VPREDICATE)
	  (match_operand:VB 3 "vector_move_operand"              "  m,  vr,  vr, Wc0, Wc1")
	  (match_operand:VB 2 "vector_undef_operand"             " vu,  vu,  vu,  vu,  vu")))]
  "TARGET_VECTOR"
  "@
   vlm.v\t%0,%3
   vsm.v\t%3,%0
   vmmv.m\t%0,%3
   vmclr.m\t%0
   vmset.m\t%0"
  "&& register_operand (operands[0], <MODE>mode)
   && register_operand (operands[3], <MODE>mode)
   && INTVAL (operands[5]) == VLMAX"
  [(set (match_dup 0) (match_dup 3))]
  ""
  [(set_attr "type" "vldm,vstm,vmalu,vmalu,vmalu")
   (set_attr "mode" "<MODE>")])

;; -------------------------------------------------------------------------------
;; ---- Predicated Broadcast
;; -------------------------------------------------------------------------------
;; Includes:
;; - 7.5. Vector Strided Instructions (zero stride)
;; - 11.16 Vector Integer Move Instructions (vmv.v.x)
;; - 13.16 Vector Floating-Point Move Instruction (vfmv.v.f)
;; - 16.1 Integer Scalar Move Instructions (vmv.s.x)
;; - 16.2 Floating-Point Scalar Move Instructions (vfmv.s.f)
;; -------------------------------------------------------------------------------

;; According to RVV ISA, vector-scalar instruction doesn't support
;; operand fetched from 2 consecutive registers, so we should use
;; vlse.v which is a memory access to broadcast a DImode scalar into a vector.
;;
;; Since the optimization flow in GCC is as follows:
;; expand --> LICM (Loop invariant) --> split.
;; To use LICM optimization, we postpone generation of vlse.v to split stage since
;; a memory access instruction can not be optimized by LICM (Loop invariant).
(define_expand "@pred_broadcast<mode>"
  [(set (match_operand:V 0 "register_operand")
	(if_then_else:V
	  (unspec:<VM>
	    [(match_operand:<VM> 1 "vector_broadcast_mask_operand")
	     (match_operand 4 "vector_length_operand")
	     (match_operand 5 "const_int_operand")
	     (match_operand 6 "const_int_operand")
	     (match_operand 7 "const_int_operand")
	     (reg:SI VL_REGNUM)
	     (reg:SI VTYPE_REGNUM)] UNSPEC_VPREDICATE)
	  (vec_duplicate:V
	    (match_operand:<VEL> 3 "direct_broadcast_operand"))
	  (match_operand:V 2 "vector_merge_operand")))]
  "TARGET_VECTOR"
{
  /* Handle vmv.s.x instruction which has memory scalar.  */
  if (satisfies_constraint_Wdm (operands[3]) || simm5_p (operands[3])
      || rtx_equal_p (operands[3], CONST0_RTX (<VEL>mode)))
    {
      if (satisfies_constraint_Wb1 (operands[1]))
        {
          // Case 1: vmv.s.x (TA) ==> vlse.v (TA)
          if (satisfies_constraint_vu (operands[2]))
            operands[1] = CONSTM1_RTX (<VM>mode);
          else if (GET_MODE_BITSIZE (<VEL>mode) > GET_MODE_BITSIZE (Pmode))
            {
	      // Case 2: vmv.s.x (TU) ==> andi vl + vlse.v (TU) in RV32 system.
	      operands[4] = gen_avl_for_scalar_move (operands[4]);
	      operands[1] = CONSTM1_RTX (<VM>mode);
	    }
          else
            operands[3] = force_reg (<VEL>mode, operands[3]);
	}
    }
  else if (GET_MODE_BITSIZE (<VEL>mode) > GET_MODE_BITSIZE (Pmode)
           && immediate_operand (operands[3], Pmode))
    operands[3] = gen_rtx_SIGN_EXTEND (<VEL>mode, force_reg (Pmode, operands[3]));
  else
    operands[3] = force_reg (<VEL>mode, operands[3]);
})

(include "autovec.md")