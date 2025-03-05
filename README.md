# CLOUDD
npm init -y
npm install express mongoose

const mongoose = require('mongoose');

const categorySchema = new mongoose.Schema({
    name: String,
    description: String
});

module.exports = mongoose.model('Category', categorySchema);
const express = require('express');
const Category = require('../models/Category');
const router = express.Router();


router.post('/categories', async (req, res) => {
    const { name, description } = req.body;
    const category = new Category({ name, description });
    await category.save();
    res.status(201).send(category);
});


router.get('/categories', async (req, res) => {
    const categories = await Category.find();
    res.status(200).send(categories);
});


router.put('/categories/:id', async (req, res) => {
    const { id } = req.params;
    const { name, description } = req.body;
    const category = await Category.findByIdAndUpdate(id, { name, description }, { new: true });
    res.status(200).send(category);
});


router.get('/categories/:id', async (req, res) => {
    const { id } = req.params;
    const category = await Category.findById(id);
    if (!category) {
        return res.status(404).send('الفئة غير موجودة');
    }
    res.status(200).send(category);
});


router.delete('/categories/:id', async (req, res) => {
    const { id } = req.params;
    await Category.findByIdAndDelete(id);
    res.status(204).send();
});

module.exports = router;
const express = require('express');
const mongoose = require('mongoose');
const categoryRoutes = require('./routes/categoryRoutes');

const app = express();
app.use(express.json());
mongoose.connect('mongodb://localhost:27017/categorydb', { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('تم الاتصال بقاعدة البيانات بنجاح'))
    .catch(err => console.error('فشل الاتصال بقاعدة البيانات', err));


app.use('/api', categoryRoutes);

const PORT = 3000;
app.listen(PORT, () => {
    console.log(`الخادم يعمل على المنفذ ${PORT}`);
});
node app.js
"scripts": {
  "start": "node app.js",
  "dev": "nodemon app.js"
}
npm run dev
