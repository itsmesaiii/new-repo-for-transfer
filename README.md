package com.cbm.classicbusinessmodel.service.impl;

import com.cbm.classicbusinessmodel.dto.PaymentResponseDTO;
import com.cbm.classicbusinessmodel.mapper.PaymentMapper;
import com.cbm.classicbusinessmodel.repository.PaymentRepository;
import com.cbm.classicbusinessmodel.service.PaymentService;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.time.LocalDate;
import java.util.List;
import java.util.stream.Collectors;

@Service
@Transactional(readOnly = true)
public class PaymentServiceImpl implements PaymentService {

    private final PaymentRepository paymentRepository;
    private final PaymentMapper paymentMapper;

    public PaymentServiceImpl(PaymentRepository paymentRepository, PaymentMapper paymentMapper) {
        this.paymentRepository = paymentRepository;
        this.paymentMapper = paymentMapper;
    }

    @Override
    public List<PaymentResponseDTO> getAllPayments() {
        return paymentRepository.findAll().stream()
                .map(paymentMapper::toPaymentResponseDTO)
                .collect(Collectors.toList());
    }

    // New method implementation
    @Override
    public List<PaymentResponseDTO> getPaymentsByDate(LocalDate date) {
        return paymentRepository.findByPaymentDate(date).stream()
                .map(paymentMapper::toPaymentResponseDTO)
                .collect(Collectors.toList());
    }
}




package com.cbm.classicbusinessmodel.controller;

import com.cbm.classicbusinessmodel.dto.PaymentResponseDTO;
import com.cbm.classicbusinessmodel.service.PaymentService;
import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.time.LocalDate;
import java.util.List;

@RestController
@RequestMapping("/api/v1/payment")
public class PaymentController {

    private final PaymentService paymentService;

    public PaymentController(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    @GetMapping("/all")
    public ResponseEntity<List<PaymentResponseDTO>> getAllPayments() {
        return ResponseEntity.ok(paymentService.getAllPayments());
    }

    // New GET endpoint for finding payments by date
    @GetMapping("/date/{date}")
    public ResponseEntity<List<PaymentResponseDTO>> getPaymentsByDate(
            @PathVariable @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate date) {
        return ResponseEntity.ok(paymentService.getPaymentsByDate(date));
    }
}
