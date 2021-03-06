function(inference_test TARGET_NAME)
  set(options "")
  set(oneValueArgs "")
  set(multiValueArgs ARGS)
  cmake_parse_arguments(inference_test "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

  set(PYTHON_TESTS_DIR ${PADDLE_SOURCE_DIR}/python/paddle/v2/fluid/tests)
  set(arg_list "")
  if(inference_test_ARGS)
    foreach(arg ${inference_test_ARGS})
      list(APPEND arg_list "_${arg}")
    endforeach()
  else()
    list(APPEND arg_list "_")
  endif()
  foreach(arg ${arg_list})
    string(REGEX REPLACE "^_$" "" arg "${arg}")
    cc_test(test_inference_${TARGET_NAME}${arg}
        SRCS test_inference_${TARGET_NAME}.cc
        DEPS ARCHIVE_START paddle_fluid ARCHIVE_END
        ARGS --dirname=${PYTHON_TESTS_DIR}/book/${TARGET_NAME}${arg}.inference.model)
    set_tests_properties(test_inference_${TARGET_NAME}${arg}
        PROPERTIES DEPENDS test_${TARGET_NAME})
  endforeach()
endfunction(inference_test)

inference_test(fit_a_line)
inference_test(image_classification ARGS vgg resnet)
inference_test(label_semantic_roles)
inference_test(recognize_digits ARGS mlp)
inference_test(recommender_system)
#inference_test(rnn_encoder_decoder)
inference_test(understand_sentiment)
inference_test(word2vec)
