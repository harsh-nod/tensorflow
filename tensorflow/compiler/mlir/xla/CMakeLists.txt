set(XLA_DIR ${TP_ROOT}/tensorflow/tensorflow/compiler/mlir/xla)
set(TF_DIR ${TP_ROOT}/tensorflow)
set(MLIR_DIR ${TP_ROOT}/llvm-project/mlir/include)

function(mlir_tablegen_xla ofn)
  tablegen(MLIR ${ARGV} "-I${MLIR_DIR}" "-I${TF_DIR}")
  set(TABLEGEN_OUTPUT ${TABLEGEN_OUTPUT} ${CMAKE_CURRENT_BINARY_DIR}/${ofn}
      PARENT_SCOPE)
endfunction()

# mlir_xla_canonicalize_gen
set(LLVM_TARGET_DEFINITIONS transforms/canonicalize.td)
mlir_tablegen_xla(transforms/generated_canonicalize.inc -gen-rewriters)
add_public_tablegen_target(mlir_xla_canonicalize_gen)

# mlir_xla_hlo_ops_gen
set(LLVM_TARGET_DEFINITIONS ir/hlo_ops.td)
mlir_tablegen_xla(ir/hlo_ops.h.inc -gen-op-decls)
mlir_tablegen_xla(ir/hlo_ops.cc.inc -gen-op-defs)
mlir_tablegen_xla(ir/hlo_structs.h.inc -gen-struct-attr-decls)
mlir_tablegen_xla(ir/hlo_structs.cc.inc -gen-struct-attr-defs)
add_public_tablegen_target(mlir_xla_hlo_ops_gen)

# mlir_xla_hlo_client_ops_gen
set(LLVM_TARGET_DEFINITIONS ir/hlo_client_ops.td)
mlir_tablegen_xla(ir/hlo_client_ops.h.inc -gen-op-decls)
mlir_tablegen_xla(ir/hlo_client_ops.cc.inc -gen-op-defs)
add_public_tablegen_target(mlir_xla_hlo_client_ops_gen)

# mlir_xla_hlo_ops_base_gen
set(LLVM_TARGET_DEFINITIONS ir/hlo_ops_base.td)
mlir_tablegen_xla(ir/hlo_ops_base.h.inc -gen-op-decls)
mlir_tablegen_xla(ir/hlo_ops_base.cc.inc -gen-op-defs)
add_public_tablegen_target(mlir_xla_hlo_ops_base_gen)

# mlir_xla_legalize_to_standard_patterns_gen
set(LLVM_TARGET_DEFINITIONS transforms/legalize_to_standard_patterns.td)
mlir_tablegen_xla(transforms/generated_legalize_to_standard.inc -gen-rewriters)
add_public_tablegen_target(mlir_xla_legalize_to_standard_patterns_gen)

# mlir_xla_lhlo_ops_gen
set(LLVM_TARGET_DEFINITIONS ir/lhlo_ops.td)
mlir_tablegen_xla(ir/lhlo_ops.h.inc -gen-op-decls)
mlir_tablegen_xla(ir/lhlo_ops.cc.inc -gen-op-defs)
add_public_tablegen_target(mlir_xla_lhlo_ops_gen)

set(XLA_SRC
  ${XLA_DIR}/convert_op_folder.cc
  ${XLA_DIR}/ir/dialect_registration.cc
  ${XLA_DIR}/ir/hlo_client_ops.cc
  ${XLA_DIR}/ir/hlo_ops.cc
  ${XLA_DIR}/ir/hlo_utils.cc
  ${XLA_DIR}/ir/lhlo_ops.cc
  ${XLA_DIR}/transforms/legalize_control_flow.cc
  ${XLA_DIR}/transforms/legalize_to_standard.cc
  ${XLA_DIR}/transforms/lower_general_dot.cc
  ${XLA_DIR}/transforms/materialize_broadcasts.cc
  ${XLA_DIR}/transforms/unfuse_batch_norm.cc
  ${XLA_DIR}/transforms/xla_legalize_to_linalg.cc
)

add_library(mlir_xla
  ${XLA_SRC}
)

add_dependencies(mlir_xla
  mlir_xla_canonicalize_gen
  mlir_xla_hlo_ops_gen
  mlir_xla_hlo_client_ops_gen
  mlir_xla_hlo_ops_base_gen
  mlir_xla_legalize_to_standard_patterns_gen
  mlir_xla_lhlo_ops_gen
)

target_include_directories(mlir_xla
  PUBLIC
  ${XLA_DIR}/ir
  ${XLA_DIR}/transforms
  ${MLIR_INCLUDE_DIR}
  ${TP_ROOT}/tensorflow
  ${ABSEIL_INC_DIR}
  ${CMAKE_BINARY_DIR}/third_party/tensorflow
)

target_link_libraries(mlir_xla
  absl::core_headers
  absl::flat_hash_set
  LLVMSupport
  MLIRAnalysis
  MLIRIR
  MLIRPass
  MLIRAnalysis
)
