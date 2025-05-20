// === Payment Functionality ===

// Backend (Node.js/Express - Add to routes/payment.js)
const express = require('express');
const router = express.Router();
const Payment = require('../models/Payment');

// POST new payment (escrow)
router.post('/', async (req, res) => {
  try {
    const payment = new Payment(req.body);
    await payment.save();
    res.status(201).send(payment);
  } catch (error) {
    res.status(400).send(error);
  }
});

// GET all payments
router.get('/', async (req, res) => {
  try {
    const payments = await Payment.find();
    res.send(payments);
  } catch (error) {
    res.status(500).send();
  }
});

module.exports = router;

// Payment Model (models/Payment.js)
const mongoose = require('mongoose');

const paymentSchema = new mongoose.Schema({
  buyerId: String,
  sellerId: String,
  amount: Number,
  status: { type: String, enum: ['pending', 'approved', 'released'], default: 'pending' },
  createdAt: { type: Date, default: Date.now }
});

module.exports = mongoose.model('Payment', paymentSchema);

// Frontend (React - PaymentForm.js)
import React, { useState } from 'react';
import axios from 'axios';

const PaymentForm = () => {
  const [payment, setPayment] = useState({ buyerId: '', sellerId: '', amount: '' });

  const handleChange = (e) => {
    setPayment({ ...payment, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    await axios.post('/api/payments', payment);
    alert('Payment submitted!');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="buyerId" onChange={handleChange} placeholder="Buyer ID" />
      <input name="sellerId" onChange={handleChange} placeholder="Seller ID" />
      <input name="amount" onChange={handleChange} placeholder="Amount" type="number" />
      <button type="submit">Submit Payment</button>
    </form>
  );
};

export default PaymentForm;

// === Shipping Integration ===

// Backend (routes/shipping.js)
const express = require('express');
const router = express.Router();

router.post('/quote', (req, res) => {
  const { origin, destination, weight } = req.body;
  // Example calculation
  const cost = weight * 2;
  res.send({ quote: cost });
});

module.exports = router;

// Frontend (ShippingQuote.js)
import React, { useState } from 'react';
import axios from 'axios';

const ShippingQuote = () => {
  const [form, setForm] = useState({ origin: '', destination: '', weight: '' });
  const [quote, setQuote] = useState(null);

  const handleChange = (e) => {
    setForm({ ...form, [e.target.name]: e.target.value });
  };

  const getQuote = async () => {
    const res = await axios.post('/api/shipping/quote', form);
    setQuote(res.data.quote);
  };

  return (
    <div>
      <input name="origin" onChange={handleChange} placeholder="Origin" />
      <input name="destination" onChange={handleChange} placeholder="Destination" />
      <input name="weight" onChange={handleChange} placeholder="Weight" />
      <button onClick={getQuote}>Get Quote</button>
      {quote && <div>Estimated Cost: ${quote}</div>}
    </div>
  );
};

export default ShippingQuote;

// === Admin Dashboard Placeholder ===

// React - AdminDashboard.js
import React from 'react';

const AdminDashboard = () => {
  return (
    <div>
      <h1>Admin Dashboard</h1>
      <p>Manage users, products, messages, and payments here.</p>
    </div>
  );
};

export default AdminDashboard;
# syrdia-frontend
